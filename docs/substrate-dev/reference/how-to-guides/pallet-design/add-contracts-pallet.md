# 添加合同托盘

本指南说明了如何将Contracts 托盘添加到您的运行时，以便您的区块链可以支持 Wasm 智能合约。您可以按照类似的模式将额外的 FRAME 托盘添加到您的运行时。但是，每个托盘都需要特定的配置设置和类型定义，以便您的运行时正确编译。

合同托盘有什么用？
从广义上讲，将 Contracts 托盘添加到运行时有两个主要原因：

您希望将智能合约部署为一等公民。

将 Contracts 托盘添加到 Substrate 运行时使您能够构建通用区块链，该区块链使用智能合约来提供您想要交付的核心功能。例如，如果您有一个专注于使用智能合约的创新想法，您可以使用 Contracts 托盘来构建您的链的基础层，然后与其他托盘补充基础层，以用最少的区块链实现特定于应用程序的目标- 特定的定制。

您希望构建一个具有灵活性的区块链，以适应用户与链的交互方式。

如果你正在构建一个不关注智能合约的链——大多数逻辑是使用其他 Substrate 托盘定义的——你仍然可能希望使用 Contracts 托盘来公开部分链逻辑以进行定制。

例如，如果您正在构建一个去中心化的交易所区块链，您可能希望让用户能够上传他们自己的交易算法。智能合约非常适合此类用例，因为它们将所有用户输入视为不受信任且可能具有对抗性。由于与执行智能合约相关的汽油费，用户必须为其交易算法的执行时间付费。

Contracts 托盘为该功能和编程语言提供了Chain 扩展原语——比如ink！— 可以利用您的链公开的业务逻辑原语。

有关合同托盘用例的更多信息，请参阅托盘自述文件。

在你开始之前
确保在您的计算机上编译了最新版本 ( polkadot-v0.9.26) 的 Substrate 节点模板。如果您还没有这样做，请参阅构建本地区块链。

导入依赖项
将合同添加到您的运行时。

要了解如何在运行时中包含合同，请参阅导入托盘。

runtime/Cargo.toml使用以下条目更新：

`pallet-contracts`
`pallet-contracts-primitives`
`pallet-contracts-rpc-runtime-api`
`pallet-contracts-rpc`
将 Contracts 托盘集成到您的运行时中，如导入托盘中所述。
将合同托盘添加到您的运行时
为您的runtime/src/lib.rs.

您应该检查您正在配置的托盘版本Cargo.toml是否与合同托盘的依赖项中指定的版本相同。

要实现特征，请定义以下特征类型：
```
impl pallet_contracts::Config for Runtime {
type Time = Timestamp;
type Randomness = RandomnessCollectiveFlip;
type Currency = Balances;
type Event = Event;
/* --snip-- */
```
有关如何为 Contracts 托盘配置特定参数的示例，请参阅runtime/src/lib.rs以下模板节点：

基板存储库节点
substrate-contracts-node是一个独立的节点。
contracts-parachain是一个平行链节点。
配置特征文档也对每种类型都有解释。

添加参数类型。

请注意，其中一些类型需要parameter_types. 要查看如何添加它们的示例，请参阅此示例。

直接在上面添加参数类型impl pallet_contracts::Config for Runtime。例如，以下代码段显示了如何添加DeletionQueueDepth参数类型：
```
parameter_types! {
 /* --snip-- */
 pub DeletionQueueDepth: u32 = ((DeletionWeightLimit::get() / (
  <Runtime as pallet_contracts::Config>::WeightInfo::on_initialize_per_queue_item(1) -
  <Runtime as pallet_contracts::Config>::WeightInfo::on_initialize_per_queue_item(0)
  )) / 5) as u32;
 /* --snip-- */
}
```

请注意参数类型如何依赖于WeightInfo. 这需要您将以下内容添加到顶部runtime/src/lib.rs：

use pallet_contracts::weights::WeightInfo;

类似地，其他参数类型使用常量，例如DAYS,MILLICENTS和AVERAGE_ON_INITIALIZE_RATIO。

runtime/src/lib.rs在其他常量存在的文件顶部定义这些：
```
// Contracts price units.
// Unit = the base number of indivisible units for balances
const UNIT: Balance = 1_000_000_000_000;
const MILLIUNIT: Balance = 1_000_000_000;
const EXISTENTIAL_DEPOSIT: Balance = MILLIUNIT;

const fn deposit(items: u32, bytes: u32) -> Balance {
 (items as Balance * UNIT + (bytes as Balance) * (5 * MILLIUNIT / 100)) / 10
}

/// We assume that ~10% of the block weight is consumed by `on_initialize` handlers.
/// This is used to limit the maximal weight of a single extrinsic.
const AVERAGE_ON_INITIALIZE_RATIO: Perbill = Perbill::from_percent(10);
```
pallet_contracts在运行时添加一个实例。

`construct_macro!`在里面创建一个 Contracts 托盘的实例runtime/src/lib.rs：
```
/* --snip-- */
Contracts: pallet_contracts,
/* --snip-- */
```
添加 API 依赖项
Contracts 托盘公开了自定义运行时 API 和 RPC 端点。

例如，有一个自定义 API 和 RPC 端点，使您能够以试运行的方式执行合约调用——也就是说，无需实际修改任何存储。这种试运行通常由链下工具（例如前端）使用来显示合约的状态。例如，如果您的应用程序部署了 ERC-20 合约并且您想要显示账户的余额，您可以配置前端以使用此自定义 API 和 RPC 端点执行余额函数的预运行。

要使用 Contracts 托盘中公开的自定义运行时 API 和 RPC 端点，您需要在运行时中包含两个额外的托盘：pallet_contracts_rpc_runtime_api和pallet_contracts_rpc.

导入依赖项。

与本指南的第一步一样，更新runtime/Cargo.toml文件以添加pallet-contracts-rpc-runtime-api.

包括pallet-contracts和pallet-contracts-rpc里面node/Cargo.toml。

这样，您的运行时可以与您的节点交互。

实现ContractsApi特征。

导航到impl_runtime_apis!运行时结束附近的宏。

在宏中添加 trait 实现：
```
impl pallet_contracts_rpc_runtime_api::ContractsApi<Block, AccountId, Balance, BlockNumber, Hash>
 for Runtime
{
// TODO: Implement the functions
}
```
对于单个特征函数的实现，您不太可能需要与模板中使用的实现不同的实现。这些功能只是将调用转发到托盘。您可以直接从Substrate复制实现。确保您从中复制的版本与Cargo.toml文件中使用的版本相同。

添加 RPC API 扩展。

默认情况下，节点的 RPC 不包含对 Contracts 托盘的访问。要与之交互，您必须扩展现有的 RPC 并将合同托盘与 API 一起添加。

在node/src/rpc.rs中，搜索此函数：
```
/// Instantiate all full RPC extensions.
pub fn create_full
```
where并在子句中添加绑定到它的以下特征：
```
C::Api: pallet_contracts_rpc::ContractsRuntimeApi<Block, AccountId, Balance, BlockNumber, Hash>,
```
在函数体中，在函数返回之前，需要添加contracts RPC API 扩展：
```
// Contracts RPC API extension
use pallet_contracts_rpc::{ContractsApiServer, ContractsRpc};
module.merge(ContractsRpc::new(client.clone()).into_rpc())?;
```
存储迁移

考虑存储迁移的最简单的方法是在您的runtime/src/lib.rs.
```
use pallet_contracts::migration;

pub struct Migrations;
impl OnRuntimeUpgrade for Migrations {
fn on_runtime_upgrade() -> Weight {
 migration::migrate::<Runtime>()
}
}
```
您还需要Executive通过添加Migrations作为类型参数来修改您的类型：
```
pub type Executive = frame_executive::Executive<
 Runtime,
 Block,
 frame_system::ChainContext<Runtime>,
 Runtime,
 AllPalletsWithSystem,
 Migrations,
>;
```
这只是如何进行存储迁移的最简单的解决方案。如果您的链中有很多状态，则迁移可能需要比构建块允许的时间更长的时间。如果迁移花费的时间超过了构建块所允许的时间，则链将停止前进并且无法恢复构建块。

启动您的升级链
您的节点模板现在包含 Contracts 托盘，并准备好执行 WebAssembly 智能合约。

使用以下命令构建并运行它：
```
# Build chain
cargo build --release

# Launch chain in development mode
./target/release/node-template --dev
```
例子
有关包含合同托盘的节点配置示例，请参阅以下存储库。

默认node配置包括与 Substrate 一起提供的所有托盘。
substrate-contracts-node是一个基于node-template.
contracts-rococo是洛可可测试网的平行链节点模板。
下一步去哪里
为什么要为智能合约使用 Rust？
为什么选择 WebAssembly 用于智能合约？.
开发智能合约
智能合约代码示例