# 铸造基本代币

本指南演示了如何利用StorageMap的原始功能来铸造代币。

在本指南中，StorageMap原语使用blake2_128_concat hasher将余额映射到帐户 ID。这种方法类似于余额托盘如何使用它来存储和跟踪帐户余额。

您应该注意，本指南仅旨在说明在 Substrate 中创建令牌的简单方法。这种方法不是推荐的最佳做法。您应牢记本指南中使用的以下限制和假设：

安全。该mint函数需要一定数量的 mint，这不是一个好的做法，因为它意味着用户可以无限制地访问存储。更安全的方法包括在运行时使用GenesisConfig或固定预定的最大值。
重量。本指南在代码片段中使用任意权重 10_000。在交易、权重和费用中了解有关权重配置的更多信息。
起源。本指南假定来源始终是sudo用户。起源是 Substrate 中的强大功能。详细了解它们在特权调用和来源中的工作方式。
有关本指南的实际实现，请参阅示例部分。

用例
让任何账户能够创建代币供应以换取原生代币费用。

步骤预览
设置Config特征。
声明您的StorageMap存储项目。
创建mint函数。
创建transfer函数。
添加检查和错误处理。
写入存储。
将新托盘添加到运行时。
设置 Config 特征
使用节点模板作为起点，指定您的托盘所依赖的类型及其Events发出的类型：
```
// TODO - this block was malformed

/* --snip-- */
pub enum Event<T: Config> {
	MintedNewSupply(T::AccountId),
	Transferred(T::AccountId, T::AccountId, T::Balance),
}
```
声明您的存储项目
此托盘仅跟踪余额到帐户 ID 的映射。叫它BalanceToAccount：
```
/* --snip-- */
#[pallet::storage]
#[pallet::getter(fn get_balance)]
pub(super) type BalanceToAccount<T: Config> = StorageMap<
	_,
	Blake2_128Concat,
	T::AccountId,
	T::Balance,
	ValueQuery
	>;
/* --snip-- */
```
创建薄荷函数
我们现在可以将注意力集中在创建托盘的预期功能上。创建mint()从任何来源发行代币供应的函数。
```
	/* --snip-- */
	#[pallet::weight(10_000 + T::DbWeight::get().writes(1))]
	pub(super) fn mint(
		origin: OriginFor<T>,
		#[pallet::compact] amount: T::Balance
		) -> DispatchResultWithPostInfo {

		let sender = ensure_signed(origin)?;

		// Check if the kitty does not already exist in our storage map
		 ensure!(Self::kitties(&kitty_id) == None, <Error<T>>::KittyExists);

		// Update storage.
		<BalanceToAccount<T>>::insert(&sender, amount);

		// Emit an event.
		Self::deposit_event(Event::MintedNewSupply(sender));

		// Return a successful DispatchResultWithPostInfo.
		Ok(().into())
	}
	/* --snip-- */
```
创建传递函数
创建transfer()函数以允许铸币帐户将给定余额转移到另一个帐户。

从写出变量开始，使用对先前在存储中声明的余额get_balance的引用：StorageMap
```
pub(super) fn transfer(
	origin: OriginFor<T>,
	to: T::AccountId,
	#[pallet::compact] amount: T::Balance,
	) -> DispatchResultWithPostInfo {
		let sender = ensure_signed(origin)?;
		let sender_balance = Self::get_balance(&sender);
		let receiver_balance = Self::get_balance(&to);
/* --snip-- */
```
添加检查和错误处理
执行余额更新时，使用checked_sub和checked_add处理溢出的潜在错误：
```
/* --snip-- */
// Calculate new balances.
let updated_from_balance = sender_balance.checked_sub(value).ok_or(<Error<T>>::InsufficientFunds)?;
let updated_to_balance = receiver_balance.checked_add(value).expect("Entire supply fits in u64, qed");
/* --snip-- */
```
写入存储
计算出新余额后，将它们的值写入存储并将事件存入当前块：
```
	// Write new balances to storage.
	<Balances<T>>::insert(&sender, updated_from_balance);
	<Balances<T>>::insert(&to, updated_to_balance);

	Self::deposit_event(RawEvent::Transfer(sender, to, value));
	Ok(())
}
/* --snip-- */
```
如果checked_sub()返回None，则该操作导致溢出并引发错误。

将您的托盘添加到运行时
如果您还不熟悉此过程，请参阅导入托盘。

例子
薄荷令牌示例托盘
奖励硬币示例托盘
相关资料
配置运行时常量
充值活动方式