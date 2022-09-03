# 实施可锁定的货币

本指南向您展示如何编写一个托盘，允许用户锁定资金以进行质押和投票。该LockableCurrency特征在通过抵押可替代资源来强制问责的经济系统中很有用。您可以使用 Substrate质押托盘以基于时间的增量管理锁定的资金。

在本指南中，我们将在我们自己的自定义托盘中实现set_lock、extend_lock和方法。remove_lock

在你开始之前
您需要在运行时中集成一个托盘才能遵循本指南。本指南假定您正在使用包含余额托盘的运行时来处理您的链的帐户和余额。您可以使用节点模板中的模板托盘来跟随。

声明必要的依赖
from 的方法LockableCurrency要求我们从frame_support.

确保您在托盘顶部导入了以下特征：

use frame_support::{
	dispatch::DispatchResult,
	traits::{Currency, LockIdentifier, LockableCurrency, WithdrawReasons},
};

声明LockIdentifier常数。

要使用LockableCurrency，您必须声明一个LockIdentifier. 鉴于它需要一个[u8; 8]类型，这个标识符必须是八个字符长。

const EXAMPLE_ID: LockIdentifier = *b"example ";

您将需要它来声明我们稍后将包含的方法。

声明您的自定义托盘类型
定义您的自定义配置类型将允许您的托盘继承我们想要实现的方法。

定义可锁定的货币类型，我们称之为StakeCurrency：

	type StakeCurrency: LockableCurrency<Self::AccountId, Moment = Self::BlockNumber>;

定义一个类型以满足我们正在使用Balance的特征的关联类型：Currency

	type BalanceOf<T> =
		<<T as Config>::StakeCurrency as Currency<<T as frame_system::Config>::AccountId>>::Balance;

这确保了我们的托盘有一个类型来处理amount我们将要实现的方法中的字段。

为您的运行时指定新类型：

	impl pallet_template::Config for Runtime {
		type Event = Event;
		type StakeCurrency = Balances; // <- add this line
	}

传入Balances可确保您的托盘方法与处理区块链账户余额的托盘LockableCurrency具有相同的理解。Balance

使用所需的方法创建可调度函数
所需的方法是：

fn lock_capital：从调用者锁定指定数量的令牌。
fn extend_lock: 延长锁定期。
fn unlock_all：释放所有锁定的令牌。
写lock_capital，使用T::StakeCurrency::set_lock：

	#[pallet::weight(10_000 + T::DbWeight::get().writes(1))]
	pub(super) fn lock_capital(
		origin: OriginFor<T>,
		#[pallet::compact] amount: BalanceOf<T>
	) -> DispatchResultWithPostInfo {

		let user = ensure_signed(origin)?;

		T::StakeCurrency::set_lock(
			EXAMPLE_ID,
			&user,
			amount,
			WithdrawReasons::all(),
		);

		Self::deposit_event(Event::Locked(user, amount));
		Ok(().into())
	}

写extend_lock，使用T::StakeCurrency::extend_lock：

	#[pallet::weight(1_000)]
	pub(super) fn extend_lock(
		origin: OriginFor<T>,
		#[pallet::compact] amount: BalanceOf<T>,
	) -> DispatchResultWithPostInfo {
		let user = ensure_signed(origin)?;

		T::StakeCurrency::extend_lock(
			EXAMPLE_ID,
			&user,
			amount,
			WithdrawReasons::all(),
		);

		Self::deposit_event(Event::ExtendedLock(user, amount));
		Ok(().into())
	}

写unlock_all，使用T::StakeCurrency::remove_lock：

	#[pallet::weight(1_000)]
	pub(super) fn unlock_all(
		origin: OriginFor<T>,
	) -> DispatchResultWithPostInfo {
		let user = ensure_signed(origin)?;

		T::StakeCurrency::remove_lock(EXAMPLE_ID, &user);

		Self::deposit_event(Event::Unlocked(user));
		Ok(().into())
	}

编写所有三个可调度的事件：

	#[pallet::event]
	#[pallet::generate_deposit(pub(super) fn deposit_event)]
	pub enum Event<T: Config> {
		Locked(T::AccountId, BalanceOf<T>),
		Unlocked(T::AccountId),
		LockExtended(T::AccountId, BalanceOf<T>),
	}

例子
lockable-currency示例托盘
相关资料
货币特征
可锁定货币
锁标识符
