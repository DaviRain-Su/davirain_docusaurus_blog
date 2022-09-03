# 添加基准

本指南说明了如何为托盘编写简单的基准测试、测试基准测试并运行基准测试命令以生成有关托盘中功能所需执行时间的实际估计。本指南不包括如何使用基准测试结果来更新交易权重。

向托盘添加基准测试
在文本编辑器中打开Cargo.toml托盘文件。
frame-benchmarking使用与托盘中其他依赖项相同的版本和分支将板条箱添加到托盘的 [依赖项]。

例如：
```
frame-benchmarking = { version = "4.0.0-dev", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.25", optional = true }
```
添加runtime-benchmarks到托盘的 [功能] 列表中。

例如：
```
[features]
runtime-benchmarks = ["frame-benchmarking/runtime-benchmarks"]
```
添加到托盘frame-benchmarking/std的特征列表。std

例如：
```
std = [
  ...
  "frame-benchmarking/std",
  ...
]
```
添加基准测试模块
benchmarking.rs在托盘文件夹中创建一个新的文本文件，例如，src。
在文本编辑器中打开benchmarking.rs文件并创建一个 Rust 模块，为您的托盘定义基准。

您可以使用benchmarking.rs任何预构建的托盘作为 Rust 模块中包含的内容的示例。通常，该模块应包含类似于以下的代码：
```
#![cfg(feature = "runtime-benchmarks")]
mod benchmarking;

use crate::*;
use frame_benchmarking::{benchmarks, whitelisted_caller};
use frame_system::RawOrigin;

benchmarks! {
  // Add individual benchmarks here
  benchmark_name {
     /* code to set the initial state */
  }: {
     /* code to test the function benchmarked */
  }
  verify {
     /* optional verification */
  }
}
```
编写单独的基准测试来测试托盘中功能的计算成本最高的路径。

基准测试宏会自动为您包含在基准测试模块中的每个基准测试生成一个测试函数。例如，宏创建类似于以下的测试函数：
```
fn test_benchmarking_[benchmark_name]<T>::() -> Result<(), &'static str>
```
Pallet-example-basic的基准测试模块提供了一些简单的示例基准测试。例如：
```
benchmarks! {
 set_dummy_benchmark {
   // Benchmark setup phase
   let b in 1 .. 1000;
 }: set_dummy(RawOrigin::Root, b.into()) // Execution phase
 verify {
	   // Optional verification phase
   assert_eq!(Pallet::<T>::dummy(), Some(b.into()))
 }
}  
```
在此示例代码中：

基准的名称是set_dummy_benchmark.
该变量b存储用于测试set_dummy函数执行时间的输入。
的值b在 1 到 1,000 之间变化，因此您可以重复运行基准测试以使用不同的输入值测量执行时间。
测试基准
在为您的托盘的基准测试模块中的宏添加基准后benchmarks!，您可以使用模拟运行时进行单元测试并确保您的基准测试函数返回Ok(())结果。

benchmarking.rs在文本编辑器中打开基准测试模块。
将impl_benchmark_test_suite!宏添加到基准测试模块的底部：
```
impl_benchmark_test_suite!(
 MyPallet,
 crate::mock::new_test_ext(),
 crate::mock::Test,
);
```
该impl_benchmark_test_suite!宏接受以下输入：

由您的托盘生成的 Pallet 结构，在此示例MyPallet中。
一个生成测试创世存储的函数，new_text_ext().
完整的模拟运行时结构，Test.
这与您用于为单元测试设置模拟运行时的信息相同。如果所有基准测试都在模拟运行时测试环境中通过，那么当您在实际运行时运行基准时，它们很可能会起作用。

通过为名为 的托盘运行类似于以下的命令，在模拟运行时执行为您的托盘生成的基准单元测试pallet-mycustom：
```
cargo test --package pallet-mycustom --features runtime-benchmarks
```
验证测试结果。
例如：
```
running 4 tests
test mock::__construct_runtime_integrity_test::runtime_integrity_tests ... ok
test tests::it_works_for_default_value ... ok
test tests::correct_error_for_none_value ... ok
test benchmarking::bench_do_something ... ok

test result: ok. 4 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```
向运行时添加基准测试
将基准添加到托盘后，您还必须更新运行时以包含托盘和托盘的基准。

在文本编辑器中打开Cargo.toml运行时文件。
将您的托盘添加到[dependencies]运行时列表中：
```
pallet-mycustom = { default-features = false, path = "../pallets/pallet-mycustom"}
```
更新[features]运行时的 以包括runtime-benchmarks您的托盘：
```
[features]
runtime-benchmarks = [
 ...
 'pallet-mycustom/runtime-benchmarks'
 ...
]
```
更新std运行时的功能以包含您的托盘：
```
std = [
 # -- snip --
 'pallet-mycustom/std'
]
```
将托盘的配置特征添加到运行时。
添加托盘的construct_runtime!宏。

如果您需要有关将托盘添加到运行时的更多详细信息，请参阅将托盘添加到运行时或导入托盘。

将您的托盘添加到功能中的define_benchmark!宏中runtime-benchmarks。
```
#[cfg(feature = "runtime-benchmarks")]
mod benches {
   define_benchmarks!(
     [frame_benchmarking, BaselineBench::<Runtime>]
     [pallet_assets, Assets]
     [pallet_babe, Babe]
     ...
     [pallet_mycustom, MyPallet]
     ...
   );
}
```
运行你的基准测试
更新运行时后，您就可以在runtime-benchmarks启用功能的情况下对其进行编译，并开始为您的托盘进行基准测试分析。

runtime-benchmarks通过运行以下命令来构建您的项目并启用该功能：
```
cargo build --package node-template --release --features runtime-benchmarks
```
查看 nodebenchmark pallet子命令的命令行选项：
```
./target/release/node-template benchmark pallet --help
```
该benchmark pallet子命令支持多个命令行选项，可帮助您自动执行基准测试。例如，您可以设置--steps和--repeat命令行选项以使用不同的值多次执行函数调用。

通过运行类似于以下的命令开始对您的托盘进行基准测试：
```
./target/release/node-template benchmark pallet \
--chain dev \
--pallet pallet_mycustom \
--extrinsic '*' \
--steps 20 \
--repeat 10 \
--output pallets/pallet-mycustom/src/weights.rs
```
此命令在指定目录中创建一个weights.rs文件。有关如何配置托盘以使用这些重量的信息，请参阅使用自定义重量。

例子
您可以将benchmarking.rs和weights.rs文件用于任何预构建的托盘，以了解有关对不同类型功能进行基准测试的更多信息。

示例托盘：基准
托盘示例：重量
天平托盘：基准
天平托盘：砝码