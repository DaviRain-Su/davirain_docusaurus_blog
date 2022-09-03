# 使用辅助函数

这个简短的指南逐步介绍了创建和重用帮助函数以在托盘代码中执行常见“验证”检查的示例。

有时，托盘内的可调度函数会重用其他可调度对象共有的逻辑。在这种情况下，将这个逻辑重构为它自己的私有函数是很有用的。在其他时候，随着代码量的增加以在可调度内执行各种检查，可调度函数变得越来越难以阅读。在这两种情况下，使用无法从托盘外部访问的辅助函数是优化代码可读性和可重用性的有用工具。在本指南中，我们将看到如何创建一个加法器帮助器来检查算术溢出，并且可以在任何可调度中重用。

创建你的辅助函数
我们将引用的助手称为fn _adder. 它检查两个类型的整数相加时没有溢出u32。

它需要两个u32整数，使用checked_add和ok_or来检查没有溢出。如果有，则返回错误；否则返回结果。

这是它作为辅助函数的样子。这将在您的托盘底部：
```
impl<T: Config> Pallet<T> {
    fn _adder(num1: u32, num2: u32) -> Result<u32, &'static str> {
        num1.checked_add(num2).ok_or("Overflow when adding")
    }
}
```
在您的可调度中使用它
确定执行加法时需要检查溢出的位置。使用辅助函数而不是重写相同的代码。下面是一个可分派的简单示例，它允许已签名的外部变量将值添加到现有存储值：
```
// Extrinsics callable from outside the runtime.
#[pallet::call]
impl<T: Config> Pallet<T> {
  #[pallet::weight(0)]
  fn add_value(
    origin: OriginFor<T>,
    val_to_add: u32
    ) -> DispatchResultWithPostInfo {
      let _ = ensure_signed(origin)?;

      ensure!(val_to_add <= T::MaxAddend::get(), "value must be <= maximum add amount constant");

      // previous value got
      let c_val = SingleValue::<T>::get();

      // checks for overflow when new value added
      let result = _adder(c_val, val_to_add);

      <SingleValue<T>>::put(result);
      Self::deposit_event(Event::Added(c_val, val_to_add, result));
      Ok(().into())
  }
}
```
例子
example-offchain-worker：add_price此托盘可调度中使用的辅助函数。
资源
checked_add在num板条箱中
ok_or在Option板条箱中
