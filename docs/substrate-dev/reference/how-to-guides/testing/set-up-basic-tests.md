# 设置基本测试

了解如何设置为您的托盘编写测试所需的脚手架。

本指南逐步介绍如何使用托盘mock.rs并test.rs作为测试托盘的基础。我们将使用节点模板作为mock.rs文件的脚手架和一个任意的托盘（称为pallet-testing）来为本指南提供一些上下文。这个托盘将包含一个名为 的函数add_value，它接受一个 origin 和 a u32，Ok(())如果值小于或等于MaxValue我们在模拟运行时指定的常量调用，则返回。

使用模板节点作为样板
在内部pallet-testing/src，我们需要做的第一件事是创建两个空文件：mock.rs和tests.rs.

粘贴来自 的内容template/src/mock.rs。我们将使用它作为我们将为我们的pallet-testing托盘定制的样板。

创建一个模拟运行时来测试您的托盘
首先修改pallet-testing/src/mock.rs以包括pallet-testing托盘。这涉及对以下代码部分的更改：
用托盘的名称替换第一行，在我们的例子中pallet_testing：
```
use crate as pallet_testing;
/*--snip--*/
```
配置模拟运行时
在frame_support::construct_runtime!中，替换pallet_template为您的托盘名称，在我们的例子中pallet_testing：
```
/*--snip--*/
TestingPallet: pallet_testing::{Pallet, Call, Storage, Event<T>},
/*--snip--*/
```
为模拟运行时实现您的托盘。替换impl pallet_template::Config for Test {...}为您的配置类型和您的托盘所需的任何常量值：
```
parameter_types! {
 pub const MaxValue: u32 = 50;
}

impl pallet_testing::Config for Test {
 type Event = Event;
 type MaxValue = MaxValue;
}
```
要使用模拟运行时，我们需要为托盘设置tests.rs文件。pallet-testing

设置和创建测试
在tests.rs中，首先导入您需要lib.rs使用的依赖项super：
```
use super::*;
```
测试错误是否按预期工作：
```
#[test]
fn error_works(){
 new_test_ext().execute_with(|| {
   assert_err!(
     TestingPallet::add_value(Origin::signed(1), 51),
     "value must be <= maximum add amount constant"
   );
 })
}
```
创建一个应该工作的测试：
```
#[test]
fn test_should_work() {
 new_test_ext().execute_with(|| {
   assert_ok!(
     TestingPallet::add_value(Origin::signed(1), 10)
   );
 })
}
```
还有一个应该失败的：
```
#[test]
fn test_should_fail() {
 new_test_ext().execute_with(|| {
   assert_ok!(
     TestingPallet::add_value(Origin::signed(1), 100)
   );
 })
}
```
运行你的测试
从您的托盘目录执行以下命令：
```
cargo test
```
例子
测试在pallet_template
reward-coin示例托盘中的测试
相关资料
单元测试
测试传递函数
assert_ok!
assert_err!
