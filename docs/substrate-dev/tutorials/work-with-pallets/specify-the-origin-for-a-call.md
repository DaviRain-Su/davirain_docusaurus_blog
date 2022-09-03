# 指定呼叫的来源
在将托盘添加到运行时中，您将函数添加pallet_nicks到了Substrate 节点模板运行时。

Nicks 托盘允许区块链用户支付押金，为他们控制的账户保留昵称。它实现了以下功能：

set_name如果该名称尚未保留，则该功能使帐户所有者能够设置他或她自己的帐户的名称。
clear_name使帐户所有者能够删除与帐户关联的名称并退还押金的功能。
kill_name不退还押金强制删除对方账户名的功能。
force_name无需存款即可为另一方设置帐户名称的功能。
本教程说明了如何使用不同的原始账户调用这些函数，以及为什么使用不同的原始账户调用这些函数很重要。

在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您已在本地安装了 Substrate 节点模板。
您已在本地安装了 Substrate 前端模板。
您已完成将托盘添加到运行时教程并成功编译了包含nicks托盘的运行时。
您通常熟悉软件开发和使用命令行界面。
教程目标
通过完成本教程，您将实现以下目标：

set_name使用有权执行调用的帐户调用该函数。
clear_name使用有权执行调用的帐户调用该函数。
force_name使用不允许执行调用的帐户调用函数。
kill_name使用没有管理权限的源调用该函数。
使用原始帐户调用该kill_name函数。Root
了解使用不同来源帐户调用函数如何导致失败或成功的结果。
识别管理帐户
正如您在向运行时添加托盘中所见，托盘的Config特征nicks声明了几种类型。对于本教程，重点是ForceOrigin类型。该ForceOrigin类型用于指定可以执行某些选项的帐户。对于此托盘，ForceOrigin类型指定可以为另一个帐户设置或删除名称的帐户。通常，只有具有管理权限的帐户（例如 root 超级用户帐户）才能代表另一个帐户进行操作。对于 Nicks 托盘，只有帐户所有者或 Root 帐户才能设置或删除保留的昵称。当您将FRAMEimpl系统Root源识别为nicks托盘管理员。例如：
```rust
type ForceOrigin = frame_system::EnsureRoot<AccountId>;
```
在节点模板的开发链规范中， Sudo 托盘被配置为使用 Alice 账户作为 FRAME 系统Root源。由于这种配置，默认情况下，只有 Alice 账户可以调用需要该ForceOrigin类型的函数。

如果您尝试使用 Alice 帐户以外的帐户调用kill_name或force_name，调用将无法执行。

设置帐户名称
为了演示呼叫的来源影响操作，让我们设置并尝试强制删除另一个帐户的帐户名称。对于此演示，请确保您拥有：

开发模式下运行的节点模板：./target/release/node-template --dev
运行并连接本地节点的前端模板：yarn start
您的浏览器连接到本地 Web 服务器：http://localhost:8000/
将前端模板中的活动账户从 Alice 更改为 Bob。
在托盘交互器中选择外部：

选择nicks托盘。
选择setName功能。
键入帐户的名称。
单击已签名以提交此由 Bob 签名的交易。
因为 Bob 是这个账户的所有者，所以交易是成功的。作为账户的所有者，Bob 还可以clearName在签名交易中执行该函数来删除该账户的昵称。

选择外部：

选择nicks托盘。
选择clearName功能。
单击已签名以提交此由 Bob 签名的交易。

因为 Bob 是这个账户的所有者，所以交易是成功的。为了让 Bob 设置或删除另一个帐户的昵称，他必须使用为托盘配置的调用forceName或killName函数。ForceOrigin

选择外部：

选择nicks托盘。
选择forceName功能。
复制并粘贴 Charlie 的帐户地址作为目标。
键入帐户的名称。
单击已签名以提交此由 Bob 签名的交易。
因为您使用 Bob 的帐户签署了此交易，所以使用Signed源而不是Root源来调度函数。在这种情况下，函数调用本身是成功的。但是，无法完成名称保留，因此会BadOrigin发出错误。


BadOrigin 错误
正如您在事件中看到的，交易导致从 Bob 的账户中提取作为提交交易的费用，但没有状态更改，因为Root源没有提交交易。更改状态失败也说明了数据库读取和写入的先验证后写原则，以确保只有成功的操作才会提交到磁盘。

使用 Root 源来调度呼叫
Sudo 托盘使您能够使用源来调度呼叫Root。在 Nick 托盘中，必须使用配置Root指定的原点调用 forceName 和 killName 函数ForceOrigin。在前端模板中，您可以Root通过单击SUDO访问 Sudo 托盘以使用源来调度呼叫。

对于此演示，请确保您拥有：

开发模式下运行的节点模板：./target/release/node-template --dev
运行并连接本地节点的前端模板：yarn start
您的浏览器连接到本地 Web 服务器：http://localhost:8000/
将活动帐户更改为 Alice。

如识别管理账户中所述Root，Alice 是在开发模式下运行链时与源关联的账户。

在托盘交互器中选择外部：

选择nicks托盘。
选择forceName功能。
复制并粘贴 Charlie 的帐户地址作为目标。
键入帐户的名称。
单击SUDO以使用来源提交此交易Root。

使用 SUDO 提交交易
选择外部：

选择nicks托盘。
选择killName功能。
复制并粘贴 Bob 的帐户地址作为目标。
单击SUDO以使用来源提交此交易Root。
在这种情况下，Sudo 托盘会发出一个Sudid事件来通知网络参与者，Root源发送了一个呼叫，但发生了错误。


提交 killName 函数发出错误
此调度错误包括两个元数据：

一个index数字，指示错误源自的托盘。
一个error数字，指示从该托盘的Error枚举发出的错误。
该index编号与宏中托盘的位置相对应，construct_runtime!宏中的第一个托盘construct_runtime!的索引号为零 (0)。

在此示例中，indexis 6（第七个托盘）和erroris 2（第三个错误）。
```rust
construct_runtime!(
pub enum Runtime where
  Block = Block,
  NodeBlock = opaque::Block,
  UncheckedExtrinsic = UncheckedExtrinsic
{
  System: frame_system,                                        // index 0
  RandomnessCollectiveFlip: pallet_randomness_collective_flip, // index 1
  Timestamp: pallet_timestamp,                                 // index 2
  Aura: pallet_aura,                                           // index 3
  Grandpa: pallet_grandpa,                                     // index 4
  Balances: pallet_balances,                                   // index 5
  Nicks: pallet_nicks,                                         // index 6
}
```

无论 的值如何index，该error值都2对应于尼克托盘中的Unnamed错误。如果 Bob 未保留昵称或之前已清除名称保留，则这是您预期的错误。

您可以确认 Alice 可以使用 SUDO 调用该killName函数来删除为当前已保留名称的任何帐户保留的昵称。

选择外部：

选择nicks托盘。
选择killName功能。
复制并粘贴 Charlie 的帐户地址作为目标。
单击SUDO以使用来源提交此交易Root。

提交成功的 killName 事务
下一步去哪里
本教程介绍了使用Rootand Signedorigins 来指定提交交易的账户，并演示了使用不同的始发账户调用函数的结果。有几个教程可以作为进一步了解 Substrate 开发的后续步骤。

除了教程之外，您可能还想探索以下资源以了解更多信息。

特权调用和来源提供了对默认原始来源类型以及如何创建自定义来源的详细了解。
事件和错误解释了如何从运行时发出事件和错误。
FRAME 托盘概述了最常用的预定义 FRAME 托盘。
