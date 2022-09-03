# 使用紧密的托盘联轴器

紧密耦合两个托盘是一种编写托盘的技术，可重用现有托盘的类型和方法。

它对于将一些运行时逻辑分解为需要访问公共类型和方法的单独托盘很有用。要了解有关托盘之间的紧耦合和松耦合的更多信息，请参阅托盘耦合。

本指南将向您展示如何使用您自己的现有托盘中的方法。

在你开始之前
本指南假设您有一个托盘要与另一个托盘连接。如果您还没有可使用的托盘，请使用模板托盘。

配置您的工作区

假设您想使用一个名为 的托盘special-pallet，它是您本地工作区中的一个托盘。Cargo.toml通过在您的托盘文件中提供其路径来导入它：
```
special-pallet = { path = '../special-pallet', default-features = false }
```
添加绑定到您的托盘的特征

现在您需要做的就是围绕您的托盘配置特征创建一个特征：
```
pub trait Config: frame_system::Config + special_pallet::Config {
   // --snip--
}
```
使用 getter 函数

要使用 from 的方法special_pallet，请按以下方式调用它：
```
// Get the members from `special-pallet` pallet
let who = special_pallet::Pallet::<T>::get();
```
上面的代码片段假定special_pallet包含一个调用的方法，该方法get()返回一个帐户 ID 向量。

例子
FRAME 的Bounties 和Tipping托盘与财政部托盘
资源
托盘联轴器
松散耦合两个托盘
