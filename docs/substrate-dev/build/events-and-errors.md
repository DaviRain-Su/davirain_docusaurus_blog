# 事件和错误

当托盘想要向外部实体（如用户、链浏览器或 dApp）发送有关运行时更改或条件的通知时，它可以发出事件。

在自定义托盘中，您可以定义：

你想发出什么类型的事件
这些事件中包含哪些信息
当这些事件被发出时
声明一个事件
运行时事件是使用`#[pallet::event]`宏创建的。例如：
```
#[pallet::event]
#[pallet::metadata(u32 = "Metadata")]
pub enum Event<T: Config> {
	/// Set a value.
	ValueSet(u32, T::AccountId),
}
```
Event枚举需要在运行时的配置特征中声明。
```
#[pallet::config]
	pub trait Config: frame_system::Config {
		/// The overarching event type.
		type Event: From<Event<Self>> + IsType<<Self as frame_system::Config>::Event>;
	}
```
向您的运行时公开事件
您在托盘中定义的任何事件都必须在/runtime/src/lib.rs文件中暴露给运行时。

向运行时公开事件：

/runtime/src/lib.rs在文本编辑器中打开文件。
为您的托盘实现Event配置特征中的类型：
```
impl template::Config for Runtime {
	 type Event = Event;
}
```
将Event类型添加到construct_runtime!宏中：
```
construct_runtime!(
	 pub enum Runtime where
 	 Block = Block,
	   NodeBlock = opaque::Block,
	   UncheckedExtrinsic = UncheckedExtrinsic
	 {
    // --snip--
	   TemplateModule: template::{Pallet, Call, Storage, Event<T>},
	   //--add-this------------------------------------->
		 }
);
```
在此示例中，事件是泛型类型并且需要`<T>`参数。`<T>`如果您的事件不使用泛型类`型，则不需要该参数。

存放事件
Substrate 提供了如何使用宏存储事件的默认实现。存放事件具有以下结构：
```
// 1. Use the `generate_deposit` attribute when declaring the Events enum.
#[pallet::event]
	#[pallet::generate_deposit(pub(super) fn deposit_event)] // <------ here ----
	#[pallet::metadata(...)]
	pub enum Event<T: Config> {
		// --snip--
	}

// 2. Use `deposit_event` inside the dispatchable function
#[pallet::call]
	impl<T: Config> Pallet<T> {
		#[pallet::weight(1_000)]
		pub(super) fn set_value(
			origin: OriginFor<T>,
			value: u64,
		) -> DispatchResultWithPostInfo {
			let sender = ensure_signed(origin)?;
			// --snip--
			Self::deposit_event(RawEvent::ValueSet(value, sender));
		}
	}
```
此函数的默认行为是deposit_event从 FRAME 系统调用，该系统将事件写入存储。

此函数将事件放置在该块的系统托盘的运行时存储中。在新块开始时，系统托盘会自动删除从前一个块中存储的所有事件。

使用默认实现存放的事件直接由下游库（如Polkadot-JS API ）支持。但是，如果您想以不同的方式处理事件，您可以实现自己的deposit_event函数。

支持的类型
事件可以使用SCALE 编解码器发出任何支持类型编码的类型。

如果您想使用类似AccountIdor的运行时泛型类型Balances，您需要包含一个where子句来定义这些类型，如上例所示。

监听事件
Substrate RPC 不直接公开用于查询事件的端点。如果您使用默认实现，您可以通过查询系统托盘的存储来查看当前块的事件列表。否则，Polkadot-JS API支持运行时事件的 WebSocket 订阅。

错误
运行时代码应该明确而优雅地处理所有错误情况。运行时代码中的函数必须是不会导致编译器恐慌的非抛出函数。编写非抛出 Rust 代码的一个常见习惯用法是编写返回Result类型的函数。Result枚举类型拥有一个变体，Err它允许函数指示它未能成功执行而无需恐慌。如果函数遇到错误，可以在 FRAME 开发环境中分派到运行时的函数调用必须返回一个可能是变体的DispatchResult类型。DispatchError

DispatchError每个 FRAME 托盘都可以使用`#[pallet::error]`宏定义一个自定义。例如：
```
#[pallet::error]
pub enum Error<T> {
		/// Error names should be descriptive.
		InvalidParameter,
		/// Errors should have helpful documentation associated with them.
		OutOfSpace,
	}
```
FRAME 支持模块还包括一个有用的ensure!宏，可用于检查先决条件并在不满足时发出错误。
```
frame_support::ensure!(param < T::MaxVal::get(), Error::<T>::InvalidParameter);
```
下一步去哪里
帧宏
Polkadot-JS API。
construct_runtime!宏
#[frame_support::pallet]宏
[pallet::error]宏