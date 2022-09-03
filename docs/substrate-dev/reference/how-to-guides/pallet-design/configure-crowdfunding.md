# 配置众筹

本指南向您展示如何构建一个控制多个令牌帐户并将数据存储在子存储中的托盘。这种结构对于构建众筹应用程序很有用。

在本指南中，我们将重点介绍子存储树的使用，它允许任何贡献者使用小型 Merkle 证明来证明他们做出了贡献。能够制作简单的贡献证明可以帮助用户获得参与众筹的奖励。

在本指南中，您将了解如何为每个不同的众筹活动创建新的子树。任何用户都可以通过指定众筹的目标金额、结束时间以及在结束时间达到目​​标时将收到集合资金的受益人来启动众筹。如果基金不成功，可以解散，将资金返还给原所有者。

在你开始之前
要遵循本指南并在您自己的托盘中实现众筹功能，请在您的托盘中包含以下依赖项：
```
use frame_support::{
	ensure,
	pallet_prelude::*,
	sp_runtime::traits::{AccountIdConversion, Hash, Saturating, Zero},
	storage::child,
	traits::{Currency, ExistenceRequirement, Get, ReservableCurrency, WithdrawReasons},
	PalletId,
};
use frame_system::{pallet_prelude::*, ensure_signed};
use super::*;
```
本指南假定您知道如何根据您创建的托盘逻辑创建自己的错误和事件。

配置您的托盘
声明您的托盘的配置特征。

除了Event类型之外，这个托盘还需要以下特征：

`Currency` - 众筹的计价货币。
`SubmissionDeposit` - 众筹所有者的存款金额。
`MinContribution` - 可以向众筹捐款的最低金额。
使用以下类型扩展您的特征：
```
/// The pallet's configuration trait.
#[pallet::config]
pub trait Config: frame_system::Config {
	type Event: From<Event<Self>> + IsType<<Self as frame_system::Config>::Event>;
	type Currency: ReservableCurrency<Self::AccountId>;
	type SubmissionDeposit: Get<BalanceOf<Self>>;
	type MinContribution: Get<BalanceOf<Self>>;
	type RetirementPeriod: Get<Self::BlockNumber>;
}
```
以及以下类型别名：
```
pub type FundIndex = u32;
type AccountIdOf<T> = <T as frame_system::Config>::AccountId;
type BalanceOf<T> = <<T as Config>::Currency as Currency<AccountIdOf<T>>>::Balance;
```
创建一个结构来存储众筹的配置。
```
#[derive(Encode, Decode, Default, PartialEq, Eq, TypeInfo)]
#[cfg_attr(feature = "std", derive(Debug))]
pub struct FundInfo<AccountId, Balance, BlockNumber> {
	/// The account that will recieve the funds if the campaign is successful.
	beneficiary: AccountId,
	/// The amount of deposit placed.
	deposit: Balance,
	/// The total amount raised.
	raised: Balance,
	/// Block number after which funding must have succeeded.
	end: BlockNumber,
	/// Upper bound on `raised`.
	goal: Balance,
}
```
还有一个新别名可以更轻松地使用它：
```
type FundInfoOf<T> =
	FundInfo<AccountIdOf<T>, BalanceOf<T>, <T as frame_system::Config>::BlockNumber>;
```
声明您的存储项目。

您的存储项目将跟踪哪个用户贡献了哪些基金以及他们贡献了多少。定义以下将用于声明您的存储项目的类型：
```
#[pallet::storage]
#[pallet::getter(fn funds)]
/// Info on all of the funds.
pub(super) type Funds<T: Config> = StorageMap<
	_,
	Blake2_128Concat,
	FundIndex,
	FundInfoOf<T>,
	OptionQuery,
>;

#[pallet::storage]
#[pallet::getter(fn fund_count)]
/// The total number of funds that have so far been allocated.
pub(super) type FundCount<T: Config> = StorageValue<_, FundIndex, ValueQuery>;
```
编写子 trie API 辅助函数
创建一个函数，为托盘的可调度项提供资金池的帐户 ID。

里面`impl<T: Config> Pallet<T>`，写：
```
pub fn fund_account_id(index: FundIndex) -> T::AccountId {
	const PALLET_ID: ModuleId = ModuleId(*b"ex/cfund");
	PALLET_ID.into_sub_account(index)
}
```
生成唯一的ChildInfo ID：
```
pub fn id_from_index(index: FundIndex) -> child::ChildInfo {
		let mut buf = Vec::new();
		buf.extend_from_slice(b"crowdfnd");
		buf.extend_from_slice(&index.to_le_bytes()[..]);

		child::ChildInfo::new_default(T::Hashing::hash(&buf[..]).as_ref())
}
```
编写以下使用Child API的辅助函数：

`pub fn contribution_put:` 在关联的子树中记录贡献put
`pub fn contribution_get:` 在关联的子树中查找贡献get
`pub fn contribution_kil:` 从关联的子树中删除贡献kill
`pub fn crowdfund_kill:` 在单个存储写入中删除相关子树中的全部贡献记录，使用`kill_storage`
编写您的可调度函数
以下步骤概述了如何为此托盘编写可调度的内容。在可调度逻辑中的各种检查之后，每个函数`Funds<T>`使用其关联的方法更改存储映射。我们的`pallet `的`create`函数也使用`FundInfo`了在第2 步中创建的结构。

创建一个新基金
`fn create:`
使用创建不平衡变量`T::Currency::withdraw`
使用步骤 2 中的结构更新Funds存储项FundInfo
存入一个Created事件
为现有基金捐款

`fn contribute:`

使用执行初步安全检查ensure!
使用T::Currency::transfer
使用辅助函数记录子树中的贡献contribution_put
存入一个Contributed事件
提取基金供款人的全部余额

`fn withdraw:`
使用执行初步安全检查`ensure!`
使用返还资金`T::Currency::resolve_into_existing`
使用子 trie 辅助函数计算新余额并更新存储空间funds，contribution_get以及contribution_kill
存款Withdrew事件
在其退休期到期后解散整个众筹。

`fn dissolve:`

使用执行初步安全检查`ensure!`
允许解散器通过使用`T::Currency::resolve_creating`解散器收集资金来收集资金
使用子 trie 辅助函数`crowdfund_kill`从存储中删除贡献者信息
存款`Dissolved`事件
向成功众筹的受益人支付款项

`fn dispense:`

用于`T::Currency::resolve_creating`收款人和来电者（分别）收款
向调用此功能的帐户提供初始存款作为清理存储的激励
`<Funds<T>>::remove(index);`使用和`Self::crowdfund_kill(index);`从存储中删除所有贡献者在一次写入中从存储中删除基金
例子
pallet_simple_crowdfund
资源
货币失衡特征
子树 API
extend_from_slice
using_encode
