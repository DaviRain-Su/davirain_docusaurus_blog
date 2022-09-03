# 调试

调试是软件开发各行各业的必需品，区块链也不例外。大多数用于通用 Rust 调试的相同工具也适用于 Substrate。

日志记录实用程序
你可以使用 Rust 的日志 API 来调试你的运行时。这带有许多宏，包括debug和info。

例如，在使用crateCargo.toml更新您的托盘文件后，只需用于登录到您的控制台：loglog::info!
```
pub fn do_something(origin) -> DispatchResult {

	let who = ensure_signed(origin)?;
	let my_val: u32 = 777;

	Something::put(my_val);

	log::info!("called by {:?}", who);

	Self::deposit_event(RawEvent::SomethingStored(my_val, who));
	Ok(())
}
```
可打印特征
Printable 特征是一种从运行时 inno_std和 in打印的方式std。该print函数适用于任何实现Printabletrait的类型。Substrate默认为某些类型（u8, u32, u64, usize, &[u8], ）实现此 trait。&str您也可以为自己的自定义类型实现它。Error这是使用节点模板作为示例代码库为托盘类型实现它的示例。
```
use sp_runtime::traits::Printable;
use sp_runtime::print;

#[frame_support::pallet]
pub mod pallet {
	// The pallet's errors
	#[pallet::error]
	pub enum Error<T> {
		/// Value was None
		NoneValue,
		/// Value reached maximum and cannot be incremented further
		StorageOverflow,
	}

	impl<T: Config> Printable for Error<T> {
		fn print(&self) {
			match self {
				Error::NoneValue => "Invalid Value".print(),
				Error::StorageOverflow => "Value Exceeded and Overflowed".print(),
				_ => "Invalid Error Case".print(),
			}
		}
	}
}

/// takes no parameters, attempts to increment storage value, and possibly throws an error
pub fn cause_error(origin) -> dispatch::DispatchResult {
	// Check it was signed and get the signer. See also: ensure_root and ensure_none
	let _who = ensure_signed(origin)?;

	print!("My Test Message");

	match Something::get() {
		None => {
			print(Error::<T>::NoneValue);
			Err(Error::<T>::NoneValue)?
		}
		Some(old) => {
			let new = old.checked_add(1).ok_or(
				{
					print(Error::<T>::StorageOverflow);
					Error::<T>::StorageOverflow
				})?;
			Something::put(new);
			Ok(())
		},
	}
}
```
使用 RUST_LOG 环境变量运行节点二进制文件以打印值。
```
RUST_LOG=runtime=debug ./target/release/node-template --dev
```
每次调用运行时函数时，这些值都会打印在终端或标准输出中。
```
2020-01-01 tokio-blocking-driver DEBUG runtime  My Test Message  <-- str implements Printable by default
2020-01-01 tokio-blocking-driver DEBUG runtime  Invalid Value    <-- the custom string from NoneValue
2020-01-01 tokio-blocking-driver DEBUG runtime  DispatchError
2020-01-01 tokio-blocking-driver DEBUG runtime  8
2020-01-01 tokio-blocking-driver DEBUG runtime  0                <-- index value from the Error enum definition
2020-01-01 tokio-blocking-driver DEBUG runtime  NoneValue        <-- str which holds the name of the ident of the error
```
请记住，将打印函数添加到运行时会增加 Rust 和 Wasm 二进制文件的大小，其中包含生产中不需要的调试代码。

Substrate 自带的打印功能
对于遗留用例，Substrate 提供了额外的Print调试（或跟踪）工具。您可以使用该print函数记录运行时执行的状态。
```
use sp_runtime::print;

// --snip--
pub fn do_something(origin) -> DispatchResult {
	print!("Execute do_something");

	let who = ensure_signed(origin)?;
	let my_val: u32 = 777;

	Something::put(my_val);

	print!("After storing my_val");

	Self::deposit_event(RawEvent::SomethingStored(my_val, who));
	Ok(())
}
// --snip--
```
使用环境变量启动链RUST_LOG以查看打印日志。
```
RUST_LOG=runtime=debug ./target/release/node-template --dev
```
如果错误被触发，这些值将打印在终端或标准输出中。
```
2020-01-01 00:00:00 tokio-blocking-driver DEBUG runtime  Execute do_something
2020-01-01 00:00:00 tokio-blocking-driver DEBUG runtime  After storing my_val
```
如果标准
遗留print功能允许您打印并实现Printable特征。但是，在某些遗留情况下，您可能想要做的不仅仅是打印，或者只是为了调试目的而不要打扰特定于 Substrate 的特征。if_std!宏对于这种情况很有用。

使用此宏的一个警告是，其中的代码只会在您实际运行本机版本的运行时时执行。
```
use sp_std::if_std; // Import into scope the if_std! macro.
```
该println!语句应该在if_std宏内部。
```
#[pallet::call]
impl<T: Config<I>, I: 'static> Pallet<T, I> {
		// --snip--
		pub fn do_something(origin) -> DispatchResult {

			let who = ensure_signed(origin)?;
			let my_val: u32 = 777;

			Something::put(my_val);

			if_std! {
				// This code is only being compiled and executed when the `std` feature is enabled.
				println!("Hello native world!");
				println!("My value is: {:#?}", my_val);
				println!("The caller account is: {:#?}", who);
			}

			Self::deposit_event(RawEvent::SomethingStored(my_val, who));
			Ok(())
		}
		// --snip--
}
```
每次调用运行时函数时，这些值都会打印在终端或标准输出中。
```
$		2020-01-01 00:00:00 Substrate Node
		2020-01-01 00:00:00   version x.y.z-x86_64-linux-gnu
		2020-01-01 00:00:00   by Anonymous, 2017, 2020
		2020-01-01 00:00:00 Chain specification: Development
		2020-01-01 00:00:00 Node name: my-node-007
		2020-01-01 00:00:00 Roles: AUTHORITY
		2020-01-01 00:00:00 Imported 999 (0x3d7a…ab6e)
		# --snip--
->		Hello native world!
->		My value is: 777
->		The caller account is: d43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d (5GrwvaEF...)
		# --snip--
		2020-01-01 00:00:00 Imported 1000 (0x3d7a…ab6e)
```