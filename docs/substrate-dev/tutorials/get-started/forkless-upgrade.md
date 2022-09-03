# 升级正在运行的网络
与许多区块链不同，Substrate 开发框架支持对作为区块链核心的运行时进行无分叉升级。大多数区块链项目都需要代码库的硬分叉，以支持新功能的持续开发或现有功能的增强。使用 Substrate，您无需硬分叉即可部署增强的运行时功能（包括重大更改）。因为运行时的定义本身就是 Substrate 链状态中的一个元素，所以网络参与者可以通过调用set_code函数来更新这个值在一次交易中。由于运行时状态的更新是使用区块链的共识机制和加密保证来验证的，因此网络参与者可以使用区块链本身来分发更新或扩展的运行时逻辑，而无需分叉链或发布新的区块链客户端。

本教程说明了如何通过将以下更改部署到现有 Substrate 运行时来执行无分叉升级：

将调度程序托盘添加到运行时。
使用调度程序托盘增加网络帐户的最低余额。
在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您已完成构建本地区块链并在本地安装了 Substrate 节点模板。
您已查看向运行时添加托盘以了解向运行时添加新托盘的介绍。
教程目标
通过完成本教程，您将实现以下目标：

使用 Sudo 托盘模拟链升级的治理。
升级运行节点的运行时以包含新托盘。
为运行时安排升级。
使用 Sudo 托盘授权升级
在 FRAME 中，Root源标识运行时管理员。只有该管理员可以通过调用该set_code函数来更新运行时。要使用源调用此函数Root，您可以使用sudoSudo 托盘中的函数来指定具有超级用户管理权限的帐户。

默认情况下，节点模板的链规范文件指定alice开发帐户是 Sudo 管理帐户的所有者。因此，本教程使用该alice帐户来执行运行时升级。

运行时升级的资源核算
分派到 Substrate 运行时的函数调用始终与权重相关联，以说明资源使用情况。FRAME 系统模块设置了这些交易可以使用的块长度和块权重的边界。但是，该set_code功能被有意设计为消耗可以放入块中的最大重量。强制运行时升级以消耗整个块可防止同一块中的事务在运行时的不同版本上执行。

该函数的权重注释set_code还指定该函数在Operational该类中，因为它提供了网络功能。被识别为可操作的函数调用：

可以消耗一个块的整个重量限制。
被给予最高优先级。
免交交易费用。
管理资源会计
在本教程中，该sudo_unchecked_weight函数用于调用该set_code函数以进行运行时升级。该sudo_unchecked_weight函数与该sudo函数相同，只是它支持一个附加参数来指定用于调用的权重。set_code此参数使您能够绕过资源记帐保护措施，为分派函数的调用指定权重为零。此设置允许块花费无限时间进行计算，以确保运行时升级不会失败，无论操作多么复杂。它可能需要成功或失败所需的所有时间。

升级运行时添加调度器托盘
节点模板在其运行时不包括调度程序托盘。为了说明运行时升级，让我们将调度程序托盘添加到正在运行的节点。

要升级运行时：

打开终端外壳。
通过运行以下命令以开发模式启动本地节点：
```shell
cargo run --release -- --dev
```
让该节点保持运行。您可以编辑和重新编译以升级运行时，而无需停止或重新启动正在运行的节点。

打开第二个终端外壳窗口或选项卡。
`substrate-node-template/runtime/Cargo.toml`在文本编辑器中打开文件。
添加调度程序托盘作为依赖项。
```toml
[dependencies]
...
pallet-scheduler = { version = "4.0.0-dev", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
...
```
将调度程序托盘添加到features列表中。
```toml
[features]
default = ["std"]
std = [
 ...
 "pallet-scheduler/std",
 ...
```
`substrate-node-template/runtime/src/lib.rs`在文本编辑器中打开文件。
添加调度程序托盘所需的类型。
```rust
parameter_types! {
 pub MaximumSchedulerWeight: Weight = 10_000_000;
 pub const MaxScheduledPerBlock: u32 = 50;
}
```
为调度程序托盘添加配置特征的实现。
```rust
impl pallet_scheduler::Config for Runtime {
 type Event = Event;
 type Origin = Origin;
 type PalletsOrigin = OriginCaller;
 type Call = Call;
 type MaximumWeight = MaximumSchedulerWeight;
 type ScheduleOrigin = frame_system::EnsureRoot<AccountId>;
 type MaxScheduledPerBlock = MaxScheduledPerBlock;
 type WeightInfo = ();
 type OriginPrivilegeCmp = EqualPrivilegeOnly;
 type PreimageProvider = ();
 type NoPreimagePostponement = ();
}
```
`construct_runtime!`在宏内添加调度程序托盘。
```rust
construct_runtime!(
 pub enum Runtime where
 Block = Block,
 NodeBlock = opaque::Block,
 UncheckedExtrinsic = UncheckedExtrinsic
 {
   /*** snip ***/
   Scheduler: pallet_scheduler,
 }
);
```
在文件顶部添加以下特征依赖项：
```rust
pub use frame_support::traits::EqualPrivilegeOnly;
```
增加[struct](https://paritytech.github.io/substrate/master/sp_version/struct.RuntimeVersion.htmlspec_version)以升级运行时版本。RuntimeVersion
```rust
pub const VERSION: RuntimeVersion = RuntimeVersion {
 spec_name: create_runtime_str!("node-template"),
 impl_name: create_runtime_str!("node-template"),
 authoring_version: 1,
 spec_version: 101,  // *Increment* this value, the template uses 100 as a base
 impl_version: 1,
 apis: RUNTIME_API_VERSIONS,
 transaction_version: 1,
};
```
查看RuntimeVersion结构的组件：

spec_name指定运行时的名称。
impl_name指定客户端的名称。
authoring_version指定块作者的版本。
spec_version指定运行时的版本。
impl_version指定客户端的版本。
apis指定支持的 API 列表。
transaction_version指定可调度函数接口的版本。
要升级运行时，您必须增加. spec_version有关详细信息，请参阅FRAME 系统模块和can_set_code功能。

保存更改并关闭substrate-node-template/runtime/src/lib.rs文件。
在第二个终端窗口或选项卡中构建更新的运行时，而不停止正在运行的节点。
```shell
cargo build --release -p node-template-runtime
```
--release命令行选项需要更长的编译时间。但是，它会生成更适合提交到区块链网络的较小构建工件。存储优化对于任何区块链都至关重要。使用此命令，构建工件将输出到target/release目录。

连接到本地节点以升级运行时以使用新的构建工件。

您可以使用Polkadot-JS 应用程序连接到本地节点。

选择 Alice 账户提交对sudoUncheckedWeight函数的调用，并setCode从system托盘中调用函数作为其参数。


选择要调用的帐户和功能
选择file upload，然后选择或拖放您为运行时生成的 WebAssembly 文件。

例如，导航以选择target/release/wbuild/node-template-runtime/node_template_runtime.compact.compressed.wasm文件。

将参数保留为_weight默认值0。


sudo 升级设置
点击提交交易。
查看授权，然后单击Sign and Submit。

交易被包含在区块中后，Polkadot-JS 应用程序中显示的版本号表示运行时版本为 now 101。


更新运行时版本 101
如果您的本地节点在终端中生成与浏览器中显示的内容匹配的块，则您已成功完成运行时升级。

接下来，我们将：

升级您的运行时版本
使用调度程序托盘在运行链上安排运行时升级
安排升级
现在节点模板已升级为包含调度程序托盘，该schedule功能可用于执行下一次运行时升级。在上一部分中，该sudo_unchecked_weight函数用于覆盖与该set_code函数关联的权重；在本节中，将安排运行时升级，以便可以将其作为块中唯一的外部进行处理。

准备升级的运行时
此升级比前一次更简单，只需要更新runtime/src/lib.rs运行时之外的单个值spec_version。
```rust
pub const VERSION: RuntimeVersion = RuntimeVersion {
 spec_name: create_runtime_str!("node-template"),
 impl_name: create_runtime_str!("node-template"),
 authoring_version: 1,
 spec_version: 102,  // *Increment* this value.
 impl_version: 1,
 apis: RUNTIME_API_VERSIONS,
 transaction_version: 1,
};

/*** snip ***/

parameter_types! {
 pub const ExistentialDeposit: u128 = 1000;  // Update this value.
 pub const MaxLocks: u32 = 50;
}

/*** snip ***/
```
此更改增加了余额托盘的价值 - 从余额托盘的ExistentialDeposit角度来看，保持帐户处于活动状态所需的最低余额。

请记住，此更改不会导致余额在 500 到 1000 之间的所有帐户都被回收 - 这将需要存储迁移，这超出了本教程的范围。

构建升级的运行时
```shell
cargo build --release -p node-template-runtime
```
这将覆盖任何以前的构建工件！因此，如果您想要手头上一个运行时 Wasm 构建文件的副本，请务必将它们复制到其他地方。

升级运行时
在上一节中，Scheduler 托盘配置了Rootorigin 作为 its ScheduleOrigin，这意味着可以使用sudo函数（not sudo_unchecked_weight）来调用schedule函数。使用此链接打开 Polkadot JS Apps UI 的Sudo选项卡：https ://polkadot.js.org/apps/#/sudo?rpc=ws://127.0.0.1:9944 。

等到所有其他字段都填写完毕后再提供when参数。将参数保留为maybe_periodic空，并将priority参数保留为其默认值0。选择系统托盘的set_code功能作为call参数，并像以前一样提供 Wasm 二进制文件。使“重量覆盖”选项保持禁用状态。一旦所有其他字段都填写完毕，使用未来大约 10 个区块（1 分钟）的区块号来填写when参数并快速提交交易。


计划升级面板
您可以使用模板节点的命令行输出或Polkadot JS 应用程序 UI 块浏览器来观察这个计划调用的发生。


计划成功运行时升级版本 102
目标区块入链后，Polkadot JS Apps UI 左上角的版本号应该反映运行时版本为 now 102。

然后，您可以通过使用Polkadot JS Apps UI Chain StateexistentialDeposit应用程序从 Balances 托盘中查询常量值来观察升级中所做的具体更改。

下一步去哪里
存储迁移

