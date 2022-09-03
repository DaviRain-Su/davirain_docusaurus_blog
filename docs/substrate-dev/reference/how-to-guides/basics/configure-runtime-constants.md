# 配置运行时常量

在运行时声明一个常量值是定义固定值或定义根据某些因素动态变化的值的有用工具。本指南向您展示如何创建用于重置u32存储值的托盘常量。这个值，我们称之为SingleValue，也可以使用一个名为 的方法来修改add_value。

配置托盘的类型、事件和错误
定义托盘中的常量。

MaxAddend将是元数据中显示的值。
ClearFrequency跟踪块编号并将用于重置SingleValue。
```
#[pallet::config]
pub trait Config: frame_system::Config {
	type Event: From<Event<Self>> + IsType<<Self as frame_system::Config>::Event>;

	#[pallet::constant] // put the constant in metadata
	/// Maximum amount added per invocation.
	type MaxAddend: Get<u32>;

	/// Frequency with which the stored value is deleted.
	type ClearFrequency: Get<Self::BlockNumber>;
}
```
声明您的存储项目和事件。

使用存储属性宏，声明SingleValue每个块周期都会修改的值。
```
#[pallet::storage]
#[pallet::getter(fn single_value)]
pub(super) type SingleValue<T: Config> = StorageValue<_, u32, ValueQuery>;
```
定义托盘的事件。
```
#[pallet::event]
#[pallet::generate_deposit(pub(super) fn deposit_event)]
pub enum Event<T: Config> {
	/// The value has been added to. The parameters are
	/// (initial amount, amount added, final amount).
	Added(u32, u32, u32),
	/// The value has been cleared. The parameter is the value before clearing.
	Cleared(u32)
}
```
添加处理操作溢出的错误：
```
#[pallet::error]
pub enum Error<T> {
	/// An operation would lead to an overflow.
	Overflow
}
```
创建托盘方法和运行时常量
配置on_finalize.

SingleValue在块执行结束时运行ClearFrequency的函数中的每个块数都设置为 0 。在属性on_finalize下指定此逻辑：`#[pallet::hooks]`
```
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
	fn on_finalize(n: T::BlockNumber) {
		if (n % T::ClearFrequency::get()).is_zero() {
			let current_value = <SingleValue<T>>::get();
			<SingleValue<T>>::put(0u32);
			Self::deposit_event(Event::Cleared(current_value));
		}
	}
}
```
创建一个允许用户指定值的方法。

只要每次调用增加的值小于该值，该add_value方法就会增加。SingleValueMaxAddend

对于此方法，您必须：

包括支票。
跟踪以前的值。
检查溢出。
更新SingleValue。
```
// Extrinsics callable from outside the runtime.
#[pallet::call]
impl<T: Config> Pallet<T> {
	#[pallet::weight(1_000)]
	fn add_value(
		origin: OriginFor<T>,
		val_to_add: u32
	) -> DispatchResultWithPostInfo {
		let _ = ensure_signed(origin)?;
		ensure!(val_to_add <= T::MaxAddend::get(), "value must be <= maximum add amount constant");
		// previous value got
		let c_val = SingleValue::<T>::get();
		// checks for overflow when new value added
		let result = c_val.checked_add(val_to_add).ok_or(Error::<T>::Overflow)?;
		<SingleValue<T>>::put(result);
		Self::deposit_event(Event::Added(c_val, val_to_add, result));
		Ok(().into())
	}
}
```
为您的运行时提供常量值。

在runtime/src/lib.rs中，声明您的托盘运行时实现的值MaxAddend和ClearFrequency：
```
parameter_types! {
	pub const MaxAddend: u32 = 1738;
	pub const ClearFrequency: u32 = 10;
}

impl constant_config::Config for Runtime {
	type Event = Event;
	type MaxAddend = MaxAddend;
	type ClearFrequency = ClearFrequency;
}
```
例子
constant-config示例托盘
资源
围裙升级
获得特质
额外常量