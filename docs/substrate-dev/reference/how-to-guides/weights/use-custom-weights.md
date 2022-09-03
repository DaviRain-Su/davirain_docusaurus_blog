# 使用自定义权重

在这里，您将了解如何从对托盘的外部参数进行基准测试的输出中使用托盘中的重量。它假定您在托盘目录中有一个名为 的文件weights.rs，其中包含运行 FRAME 的基准测试工具自动生成的权重。

目标
在托盘的外部使用由FRAME 的基准测试工具生成的重量。

用例
使用从基准测试中生成的准确权重用于托盘的外在因素。

脚步
1.WeightInfo在您的托盘weight.rs文件中定义特征
为了在您的托盘中使用由基准测试工具生成的文件（此处weights.rs是 Substrate 权重的示例），请定义一个特征，以便轻松访问其功能。例如：pallet-example
```
pallets/example/src/weights.rs

pub trait WeightInfo {
  fn example(len: usize,) -> Weight;
}
```

2. 包含WeightInfo在您的托盘中
在您的托盘中，在顶部 lib.rs声明并介绍以将特征暴露给您的托盘。然后，将类型引入托盘的特征：
```
pub mod weights;
pub use crate::weights::WeightInfo;WeightInfoWeightInfoConfig

pallets/example/src/lib.rs

pub mod weights;
pub use weights::*;

// -- snip --

pub trait Config: frame_system::Config {
    // -- snip --

    /// Information on runtime weights.
    type WeightInfo: WeightInfo;
}
```
3. 编写自定义重量声明
对于您的每个可分派，引入适当的权重行来确定使用配置WeightInfo类型的权重。例如，T::WeightInfo::example将是从外部返回weights.rs的权重函数：example
```
pallets/example/src/lib.rs

#[pallet::weight(T::WeightInfo::example(x.len()))]
fn example(origin: OriginFor<T>, arg: Vec<u32>) -> DispatchResult {
  // -- snip --
}
```
4.在trait impls中定义WeightInfo类型Config
您需要WeightInfo在托盘的测试模型mock.rs 和 Substratenode的运行时中定义特征。

在您的托盘的Configtrait impl 中mock.rs，引入以下行以在我们的测试中排除权重。

在pallets/example/src/mock.rs或pallets/example/src/test.rs：
```
impl Config for TestRuntime {
  // -- snip --
  type WeightInfo = ();
}
```
在您的托盘的Configtrait impl in 中runtime/src/lib.rs，引入以下行以在我们的运行时包含权重。
```
impl pallet_example::Config for Runtime {
  // -- snip --
  type WeightInfo = pallet_example::weights::SubstrateWeight<Runtime>;
}
```
重新编译节点后，您的外部节点现在将使用 pallets/example/src/weights.rs.

例子
示例托盘中的基准测试
示例托盘中的重量
相关资料
基准测试
计算费用
添加基准