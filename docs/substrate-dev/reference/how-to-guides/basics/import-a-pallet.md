# 导入托盘

本指南演示了如何在运行时中快速集成本地和外部托盘。有关更详细的分步说明，请参阅将托盘添加到运行时。

本指南说明：

如何包含实现事件和调用运行时的自定义本地托盘。
如何Crates.io在运行时中包含一个外部托盘。
创建本地托盘
创建一个名为pallet_something.
通过将以下内容添加到以下内容来导入此托盘/runtime/src/lib.rs：
```
// Import your pallet.
pub use pallet_something;
```
配置托盘的运行时实现。假设本地托盘仅具有暴露给运行时的Event和类型。Call将以下内容添加到/runtime/src/lib.rs：
```
// Configure your pallet.
impl pallet_something::Config for Runtime {
	type Event = Event;
	type Call = Call;
}
```
声明您的货盘及其暴露的物品。包括运行时宏的附加Pallet和Storage类型。在中，添加以下内容：construct_runtime!
```
construct_runtime!(
	pub enum Runtime where
	Block = Block,
	NodeBlock = opaque::Block,
	UncheckedExtrinsic = UncheckedExtrinsic
	{
		/* --snip-- */
		Something: pallet_something::{Pallet, Call, Storage, Event<T>},
		/* --snip-- */
	}
);
```
更新/runtime/Cargo.toml

在/runtime/Cargo.toml中，将您的托盘作为本地依赖项包含在std并添加runtime-benchmarks. 例如：
```
# --snip--
pallet-something = { default-features = false,   path = '../pallets/something'
version = '3.0.0'
# --snip--
[features]
default = ['std']
runtime-benchmarks = [
 # --snip--
 'pallet-something/runtime-benchmarks',
]
std = [
 'pallet-something/std',
 # --snip--
]
```
导入外部托盘
要添加外部货盘，您可以使用与本地货盘类似的方法，但您必须包括货盘公开的所有类型。您还必须包括相关的参数类型和常量。有关如何声明参数和常量的示例，请参阅pallet_timestamp.

/runtime/Cargo.toml下面是一个示例，如果托盘托管在crates.parity.io上，您将如何将外部托盘添加到依赖项：
```
[dependencies]
pallet-external = {default-features = false, git = "https://github.com/paritytech/substrate.git", version = "4.0.0-dev"}

# --snip--
runtime-benchmarks = [
  /* --snip */
  'pallet-external/runtime-benchmarks',
]
std = [
  'pallet-external/std',
  # --snip--
]
```
例子
模板托盘
时间戳托盘
相关资料
Timestamp Pallet 相关类型
框架pallet-timestamp
单元测试