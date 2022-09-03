# 测试传递函数
测试每个功能是开发用于生产的托盘的重要部分。transfer本指南将引导您了解为基本功能编写测试用例的最佳实践。

概述transfer功能
转移函数有两个关键要素：从一个账户中减去一个余额并将该余额添加到另一个账户。在这里，我们将首先概述这个函数：
```
#[pallet::weight(10_000)]
pub (super) fn transfer(
  origin: OriginFor<T>,
  to: T::AccountId,
  #[pallet::compact] amount: T::Balance,
  ) -> DispatchResultWithPostInfo {
    let sender = ensure_signed(origin)?;

    Accounts::<T>::mutate(&sender, |bal| {
      *bal = bal.saturating_sub(amount);
      });
      Accounts::<T>::mutate(&to, |bal| {
        *bal = bal.saturating_add(amount);
        });

    Self::deposit_event(Event::<T>::Transferred(sender, to, amount))
    Ok(().into())
}
```
检查发件人是否有足够的余额
首先要验证的是发件人是否有足够的余额。在一个单独的tests.rs文件中，写出第一个测试用例：
```
#[test]
fn transfer_works() {
  new_test_ext().execute_with(|| {
    MetaDataStore::<Test>::put(MetaData {
			issuance: 0,
			minter: 1,
			burner: 1,
		});
    // Mint 42 coins to account 2.
    assert_ok!(RewardCoin::mint(Origin::signed(1), 2, 42));
    // Send 50 coins to account 3.
    asset_noop!(RewardCoin::transfer(Origin::signed(2), 3, 50), Error::<T>::InsufficientBalance);
```
配置错误处理
要实现一些错误检查，请替换mutate为try_mutate使用ensure!. 这将检查bal 是否大于或等于 amount，如果不是则抛出错误消息：
```
Accounts::<T>::try_mutate(&sender, |bal| {
  ensure!(bal >= amount, Error::<T>::InsufficientBalance);
  *bal = bal.saturating_sub(amount);
  Ok(())
});
```
cargo test从您的托盘目录运行。

检查发送帐户是否低于最低余额
在你的transfer_works函数里面：
```
assert_noop!(RewardCoin::transfer(Origin::signed(2), 3, 50), Error::<Test>::InsufficientBalance);
```
检查两个测试是否一起工作
用于#[transactional]生成两个检查的包装器：
```
#[transactional]
pub(super) fn transfer(
/*--snip--*/
```
处理灰尘帐户
确保发送和接收帐户不是灰尘帐户。使用T::MinBalance::get()：
```
/*--snip--*/
let new_bal = bal.checked_sub(&amount).ok_or(Error::<T>::InsufficientBalance)?;
ensure!(new_bal >= T::MinBalance::get(), Error::<T>::BelowMinBalance);
/*--snip--*/
```
例子
在reward-coin示例托盘中进行测试。
资源
assert_ok!
assert_noop!
ensure!
try_mutate
