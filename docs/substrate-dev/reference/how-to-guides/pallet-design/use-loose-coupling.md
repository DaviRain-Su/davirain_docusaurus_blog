# 使用松散托盘联轴器

本指南逐步介绍如何使用称为松散耦合的技术重用另一个托盘中的函数或类型。

松散耦合是一种能够重用来自托盘内另一个托盘的逻辑的技术。在本指南中，我们展示了在工作托盘中使用来自外部托盘的类型的简单模式，方法是在托盘的配置特征中使用特征边界。我们将松散地耦合一个托盘以利用`frame_support`的 `Currency` 特征。

配置您的工作区
在Cargo.toml工作目录中的托盘文件中，确保您指定要相应地耦合到的托盘：
```
[dependencies]
frame-support = { default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26"}

# -- snip

[features]
default = ['std']
std = [
  'frame-support/std',
# -- snip
]
```
导入要使用的特征
在这个例子中，我们想要使用Currencytrait fromframe_support以便我们可以让我们的托盘访问它的方法。

在您的托盘中导入特征：
```
use frame_support::traits::Currency;
```
Config为您的托盘特征创建一个类型
在您的配置特征中，创建一个类型，该类型受您要公开给您的货盘的类型（在 中this-pallet/src/lib.rs）的约束：
```
pub trait Config: frame_system::Config {
   // --snip--

   /// A type that is accessing our loosely coupled pallet `my-pallet`
   type LocalCurrency: Currency<Self::AccountId>;
}
```
使用松耦合托盘类型提供的方法（在 中this-pallet/src/lib.rs）：
```
// Use the getter from `my-pallet`
let total_balance = T::LocalCurrency::total_issuance();
```
在上面的代码片段中，我们使用total_issuance 了 Currency trait 从frame_support.

在运行时配置中提供实现
在我们的运行时配置中，通常runtime/src/lib.rs我们指定LocalCurrencyto Balances，它在construct_runtime!宏内部定义并且具有pallet_balances 实现trait的Currency类型。
```
impl my_pallet::Config for Runtime {
  type LocalCurrency = Balances;
}

construct_runtime! (
  pub enum Runtime where
  Block = Block,
  NodeBlock = opaque::Block,
  UncheckedExtrinsic = UncheckedExtrinsic
  {
    Balances: pallet_balances::{Pallet, Call, Storage, Config<T>, Event<T>},
  }
)
```
例子
try_origin来自EnsureOriginFRAME民主托盘中的特征
在所有 FRAME 托盘中的使用WeightInfo，例如身份托盘及其 特定称重方法的使用
使用的KeyOwnerProofSystem特征 pallet_grandpa
资源
托盘联轴器
紧密连接两个托盘