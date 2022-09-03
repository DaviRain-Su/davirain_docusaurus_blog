# 使用条件权重

Substrate 提供了一种称为事务加权的机制来量化执行事务时消耗的资源。通常，我们为此使用从基准测试返回的权重函数。但是 Substrate 还允许我们根据特定条件应用完全不同的权重函数。我们将在本指南中介绍一个示例。一旦定义，它可以直接在你的托盘中使用，如下所示：
```
#[pallet::weight(Conditional(\<your condition\>)
```
目标
在您的托盘中创建和使用自定义权重。
根据特定条件应用不同的权重函数计算外在权重值。
以下是我们将要实现的不同特征：

`WeighData` : 对函数中的数据进行加权。`pallet::weight` 期望任何实现 `WeighData` 用调度参数的元组替换 `T`。
`PaysFee` : 指定派送是否支付费用。
`ClassifyDispatch` : 一种告诉运行时正在执行的调度类型的方法。
脚步
1. 编写WeighData结构
Import DispatchClass，WeighData并 在您的托盘顶部PaysFee声明。use frame_support::dispatch::{DispatchClass, PaysFee, WeighData}

在您的托盘lib.rs文件中，声明一个名为的结构Conditional并编写 for 的实现WeighData，Conditional其中第一个参数是评估为布尔值的条件。在以下示例中，如果条件为真，则权重将与输入呈线性关系。否则重量将是一个常数。
```
pallets/example/src/lib.rs
```
```
use frame_support::dispatch::{DispatchClass, PaysFee, WeighData};

// -- snip --

pub struct Conditional(u32);

impl WeighData<(&bool, &u32)> for Conditional {
  fn weigh_data(&self, (switch, val): (&bool, &u32)) -> Weight {

    // If the first parameter is true, then the weight is linear in the second parameter.
    if *switch {
      val.saturating_mul(self.0)
      }
      // Otherwise the weight is constant.
    else {
      self.0
    }
  }
}
```
2. 分类调度调用
添加weights::{ClassifyDispatch, DispatchClass, Pays, Weight}到您的托盘的frame_support进口。由于此实现WeighData需要一个DispatchClass, 用于default 将所有调用分类为正常：
```
pallets/example/src/lib.rs
```
```
use frame_support::weights::{ClassifyDispatch, DispatchClass, Pays, Weight};

// -- snip --

// Implement ClassifyDispatch
impl<T> ClassifyDispatch<T> for Conditional {
  fn classify_dispatch(&self, _: T) -> DispatchClass {
    Default::default()
  }
}
```

3. 实施PaysFee
指定PaysFee自定义WeighData结构的使用方式。将此设置为true将要求此调度的调用者支付费用：
```
pallets/example/src/lib.rs
```
```
use frame_support::weights::PaysFee

// -- snip --

// Implement PaysFee
impl PaysFee for Conditional {
    fn pays_fee(&self) -> bool {
      true
    }
}
```
4. 将权重结构用于外部
在托盘的外部使用条件权重结构，如下所示：
`pallets/example/src/lib.rs`

```
  #[pallet::weight(Conditional(200))]
  fn example(origin: OriginFor<T>, add_flag: bool, val: u32>) -> DispatchResult {
      //...
  }
```

例子
WeightForSetDummy示例托盘中的自定义结构
相关资料
基准测试
计算费用
使用自定义权重