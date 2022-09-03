# 配置合约托盘
如果您完成了构建本地区块链教程，那么您已经知道 Substrate节点模板提供了一个工作运行时，其中包括一些供您入门的托盘。在将托盘添加到运行时中，您了解了将新托盘添加到运行时的基本常用步骤。但是，每个托盘都需要您配置特定的参数和类型。为了了解这意味着什么，本教程演示了向运行时添加更复杂的托盘。在本教程中，您将添加Contracts 托盘，以便您可以支持区块链的智能合约开发。

如果您完成了将托盘添加到运行时教程，您会在添加合同托盘时注意到一些熟悉的模式。本教程较少关注那些常见模式，而更多地关注添加合同托盘所需的设置。

在你开始之前
在开始本教程之前，请验证以下内容：

您已经下载并编译了Substrate 节点模板polkadot-v0.9.26的版本 。
您已按照 构建本地区块链中的说明下载并安装了 Substrate 前端模板。
添加托盘依赖项
任何时候将托盘添加到运行时，都需要导入适当的 crate 并更新运行时的依赖项。对于 Contracts 托盘，您需要导入pallet-contractscrate。

要导入pallet-contracts板条箱：

打开终端外壳并切换到节点模板的根目录。
runtime/Cargo.toml在文本编辑器中打开配置文件。
找到该[dependencies]部分并注意如何导入其他托盘。
复制现有的托盘依赖描述并将托盘名称替换为pallet-contracts以使托盘可用于节点模板运行时。

例如，添加类似于以下内容的行：
```toml
pallet-contracts = { version = "4.0.0-dev", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
```
通过将 crate 添加到依赖项列表中，导入pallet-contracts-primitivescrate 以使其可用于节点模板运行时。

在大多数情况下，您为节点模板的任何给定版本中的每个托盘指定相同的信息。但是，如果编译器指示找到与您指定的版本不同的版本，您可能需要修改依赖项以匹配编译器识别的版本。例如，如果编译器为pallet-contracts-primitivescrate 找到 6.0.0 版本：
```toml
pallet-contracts-primitives = { version = "6.0.0", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
```
将合同托盘添加到std功能列表中，以便在将运行时构建为平台原生二进制文件时包含其功能。
```toml
[features]
default = ["std"]
std = [
  "codec/std",
  "scale-info/std",
  "frame-executive/std",
  "frame-support/std",
  "frame-system-rpc-runtime-api/std",
  "frame-system/std",
  "pallet-aura/std",
  "pallet-balances/std",
  "pallet-contracts/std",
  "pallet-contracts-primitives/std",
]
```

保存更改并关闭runtime/Cargo.toml文件。
通过运行以下命令检查您的运行时是否正确编译：
```shell
cargo check -p node-template-runtime
```
实现 Contracts 配置特征
现在您已成功导入智能合约的托盘，您可以在运行时实现参数和类型。如果您浏览过其他教程，您可能已经知道每个托盘都有一个配置特征 - 称为Config- 运行时必须实现。

如果您查看 Rust API 文档pallet_contracts::Config，您会注意到 - 与 Nicks 托盘不同 - 此托盘具有许多关联类型，因此本教程中的代码将比以前的教程更复杂。

要Config在运行时实现 Contracts 托盘的特征：

runtime/src/lib.rs在文本编辑器中打开文件。
找到pub use frame_support块并添加Nothing到特征列表中。

例如：
```rust
pub use frame_support::{
construct_runtime, parameter_types,
traits::{
  ConstU128, ConstU32, ConstU64, ConstU8, KeyOwnerProofSystem, Randomness, StorageInfo,Nothing
},
weights::{
 constants::{BlockExecutionWeight, ExtrinsicBaseWeight, RocksDbWeight, WEIGHT_PER_SECOND},
 IdentityFee, Weight,
},
StorageValue,
};
```
添加一行以从合同托盘导入默认合同重量。例如：
```rust
pub use frame_system::Call as SystemCall;
pub use pallet_balances::Call as BalancesCall;
pub use pallet_timestamp::Call as TimestampCall;
use pallet_transaction_payment::CurrencyAdapter;
use pallet_contracts::DefaultContractAccessWeight; // Add this line
```
将 Contracts 托盘所需的常量添加到运行时。

例如：
```rust
/* After this block */
// Time is measured by number of blocks.
pub const MINUTES: BlockNumber = 60_000 / (MILLISECS_PER_BLOCK as BlockNumber);
pub const HOURS: BlockNumber = MINUTES * 60;
pub const DAYS: BlockNumber = HOURS * 24;

/* Add this block */
// Contracts price units.
pub const MILLICENTS: Balance = 1_000_000_000;
pub const CENTS: Balance = 1_000 * MILLICENTS;
pub const DOLLARS: Balance = 100 * CENTS;

const fn deposit(items: u32, bytes: u32) -> Balance {
items as Balance * 15 * CENTS + (bytes as Balance) * 6 * CENTS
}
const AVERAGE_ON_INITIALIZE_RATIO: Perbill = Perbill::from_percent(10);
/*** End Added Block ***/
```
添加参数类型并在运行时实现Config特征。pallet_contracts

例如：
```rust
/*** Add a block similar to the following ***/
parameter_types! {
  pub const DepositPerItem: Balance = deposit(1, 0);
  pub const DepositPerByte: Balance = deposit(0, 1);
  pub const DeletionQueueDepth: u32 = 128;
  pub DeletionWeightLimit: Weight = AVERAGE_ON_INITIALIZE_RATIO * BlockWeights::get().max_block;
  pub Schedule: pallet_contracts::Schedule<Runtime> = Default::default();
}

impl pallet_contracts::Config for Runtime {
  type Time = Timestamp;
  type Randomness = RandomnessCollectiveFlip;
  type Currency = Balances;
  type Event = Event;
  type Call = Call;
  type CallFilter = frame_support::traits::Nothing;
  type WeightPrice = pallet_transaction_payment::Pallet<Self>;
  type WeightInfo = pallet_contracts::weights::SubstrateWeight<Self>;
  type ChainExtension = ();
  type Schedule = Schedule;
  type CallStack = [pallet_contracts::Frame<Self>; 31];
  type DeletionQueueDepth = DeletionQueueDepth;
  type DeletionWeightLimit = DeletionWeightLimit;
  type DepositPerByte = DepositPerByte;
  type DepositPerItem = DepositPerItem;
  type AddressGenerator = pallet_contracts::DefaultAddressGenerator;
  type ContractAccessWeight = DefaultContractAccessWeight<BlockWeights>;
  type MaxCodeLen = ConstU32<{ 256 * 1024 }>;
  type RelaxedMaxCodeLen = ConstU32<{ 512 * 1024 }>;
  type MaxStorageKeyLen = ConstU32<{ 512 * 1024 }>;
}
/*** End added block ***/
```
有关合约托盘的配置以及类型和参数的使用方式的更多信息，请参阅合约托盘源代码。

添加pallet_contracts到construct_runtime!宏。

例如：
```rust
// Create the runtime by composing the FRAME pallets that were previously configured
construct_runtime!(
  pub enum Runtime where
    Block = Block,
    NodeBlock = opaque::Block,
    UncheckedExtrinsic = UncheckedExtrinsic
  {
     System: frame_system,
     RandomnessCollectiveFlip: pallet_randomness_collective_flip,
     Timestamp: pallet_timestamp,
     Aura: pallet_aura,
     Grandpa: pallet_grandpa,
     Balances: pallet_balances,
     TransactionPayment: pallet_transaction_payment,
     Sudo: pallet_sudo,
     Contracts: pallet_contracts,
  }
);
```
保存更改并关闭runtime/src/lib.rs文件。
通过运行以下命令检查您的运行时是否正确编译：
```shell
cargo check -p node-template-runtime
```
尽管运行时应该编译，但您还不能编译整个节点。

公开合约 API
一些托盘（包括合同托盘）公开了自定义运行时 API 和 RPC 端点。您无需启用 Contracts 托盘上的 RPC 调用即可在链上使用它。但是，公开 Contracts 托盘的 API 和端点很有用，因为这样做可以让您执行以下任务：

从链下读取合约状态。
在不进行事务的情况下调用节点存储。
要公开 Contracts RPC API：

runtime/Cargo.toml在文本编辑器中打开文件。
pallet-contracts-rpc-runtime-api将托盘的描述添加到[dependencies]使用与其他托盘相同的版本和分支信息的部分。

例如：
```toml
pallet-contracts-rpc-runtime-api = { version = "4.0.0-dev", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
```
添加pallet-contracts-rpc-runtime-api到std功能列表中，以便在将运行时构建为本机二进制文件时包含其功能。
```toml
[features]
default = ["std"]
std = [
  "codec/std",
  ...
  "pallet-contracts/std",
  "pallet-contracts-primitives/std",
  "pallet-contracts-rpc-runtime-api/std",
  ...
]
```
保存更改并关闭runtime/Cargo.toml文件。
在文本编辑器中打开runtime/src/lib.rs文件并通过添加以下常量启用合约调试：
```rust
const CONTRACTS_DEBUG_OUTPUT: bool = true;
```
打开文件并在运行时文件末尾附近的宏runtime/src/lib.rs中实现合约运行时 API 。impl_runtime_apis!lib.rs

例如在impl_runtime_apis! { }该部分中：
```rust
/*** Add this block ***/
impl pallet_contracts_rpc_runtime_api::ContractsApi<Block, AccountId, Balance, BlockNumber, Hash>
 for Runtime
 {
  fn call(
     origin: AccountId,
     dest: AccountId,
     value: Balance,
     gas_limit: u64,
     storage_deposit_limit: Option<Balance>,
     input_data: Vec<u8>,
  ) -> pallet_contracts_primitives::ContractExecResult<Balance> {
     Contracts::bare_call(origin, dest, value, gas_limit, storage_deposit_limit, input_data, CONTRACTS_DEBUG_OUTPUT)
  }
  
  fn instantiate(
     origin: AccountId,
     value: Balance,
     gas_limit: u64,
     storage_deposit_limit: Option<Balance>,
     code: pallet_contracts_primitives::Code<Hash>,
     data: Vec<u8>,
     salt: Vec<u8>,
  ) -> pallet_contracts_primitives::ContractInstantiateResult<AccountId, Balance> {
     Contracts::bare_instantiate(origin, value, gas_limit, storage_deposit_limit, code, data, salt, CONTRACTS_DEBUG_OUTPUT)
     }
     
  fn upload_code(
     origin: AccountId,
     code: Vec<u8>,
     storage_deposit_limit: Option<Balance>,
  ) -> pallet_contracts_primitives::CodeUploadResult<Hash, Balance> {
     Contracts::bare_upload_code(origin, code, storage_deposit_limit)
  }
  
  fn get_storage(
     address: AccountId,
     key: Vec<u8>,
     ) -> pallet_contracts_primitives::GetStorageResult {
     Contracts::get_storage(address, key)
     }
  }
```
保存更改并关闭runtime/src/lib.rs文件。
通过运行以下命令检查您的运行时是否正确编译：
```shell
cargo check -p node-template-runtime
```
更新外部节点
至此，您已完成将 Contracts 托盘添加到运行时。现在，您需要考虑外部节点是否需要任何相应的更新。Substrate 提供了一个 RPC 服务器来与节点交互。但是，默认的 RPC 服务器不提供对 Contracts 托盘的访问。要与合同托盘交互，您必须扩展现有的 RPC 服务器以包含合同托盘和合同 RPC API。要让 Contracts 托盘利用 RPC 端点 API，您需要将自定义 RPC 端点添加到外部节点配置中。

要将 RPC API 扩展添加到外部节点：

在文本编辑器中打开node/Cargo.toml文件，然后将 Contracts 和 Contracts RPC crates 添加到这些[dependencies]部分。

例如：
```toml
pallet-contracts = { version = "4.0.0-dev", git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
pallet-contracts-rpc = { version = "4.0.0-dev", git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26" }
```
因为您已经公开了运行时 API，并且现在正在为外部节点编写代码，所以您不需要使用no_std配置，因此您不必维护专用std的功能列表。

保存更改并关闭node/Cargo.toml文件。
在文本编辑器中打开node/src/rpc.rs文件并找到以下行。
```rust
use node_template_runtime::{opaque::Block, AccountId, Balance, Index};
```
添加BlockNumber和Hash到现有的use node_template_runtime声明。
```rust
use node_template_runtime::{opaque::Block, AccountId, Balance, Index, BlockNumber, Hash}; 
```
添加use pallet_contracts_rpc到文件中。
```rust
use pallet_transaction_payment_rpc::{TransactionPayment, TransactionPaymentApiServer};
use substrate_frame_rpc_system::{System, SystemApiServer};
use pallet_contracts_rpc::{Contracts, ContractsApiServer}; // Add this line
```
将 Contracts RPC 托盘添加到create_fullRPC 扩展的函数中。
```rust
/// Instantiate all full RPC extensions.
pub fn create_full<C, P>(
  deps: FullDeps<C, P>,
) -> Result<RpcModule<()>, Box<dyn std::error::Error + Send + Sync>>
where
  C: ProvideRuntimeApi<Block>,
  C: HeaderBackend<Block> + HeaderMetadata<Block, Error = BlockChainError> + 'static,
  C: Send + Sync + 'static,
  C::Api: substrate_frame_rpc_system::AccountNonceApi<Block, AccountId, Index>,
  C::Api: pallet_transaction_payment_rpc::TransactionPaymentRuntimeApi<Block, Balance>,
  C::Api: pallet_contracts_rpc::ContractsRuntimeApi<Block, AccountId, Balance, BlockNumber, Hash>, // Add this line
  C::Api: BlockBuilder<Block>,
  P: TransactionPool + 'static,
```
添加 Contracts RPC API 的扩展。
```rust
module.merge(System::new(client.clone(), pool.clone(), deny_unsafe).into_rpc())?;
module.merge(TransactionPayment::new(client.clone()).into_rpc())?;
module.merge(Contracts::new(client.clone()).into_rpc())?; // Add this line
```
保存更改并关闭node/src/rpc.rs文件。
通过运行以下命令检查您的运行时是否正确编译：
```shell
cargo check -p node-template
```
如果没有错误，您就可以编译了。

通过运行以下命令以发布模式编译节点：
```shell
cargo build --release
```
启动本地 Substrate 节点
在您的节点编译后，您就可以启动已通过Contracts 托盘中的智能合约功能增强的 Substrate 节点，并使用前端模板与其交互。

启动本地节点：

如有必要，打开终端外壳。
切换到 Substrate 节点模板的根目录。
通过运行以下命令以开发模式启动节点：
```shll
./target/release/node-template --dev
```
通过查看终端中显示的输出来验证您的节点是否已启动并成功运行。

如果finalized控制台输出中的数字在增加，则您的区块链正在生成新块并就它们描述的状态达成共识。

在您的计算机上打开一个新的终端外壳。
在新终端中，切换到安装前端模板的根目录。
通过运行以下命令启动前端模板的 Web 服务器：
```sell
yarn start
```
在浏览器中打开http://localhost:8000/以查看前端模板。
在 Pallet Interactor 组件中，确认选择了 Extrinsic。
contracts从可调用的托盘列表中选择。


查看合同托盘
下一步
在本教程中，您学习了：

如何导入合同托盘。
如何公开 Contracts 托盘 RPC 端点 API。
如何更新外部节点。
如何使用前端模板在运行时验证 Contracts 托盘是否可用。
要开始使用合同托盘，您需要开始编写一些要部署的智能合同。探索以下主题和教程以了解更多信息。

自定义 RPC
准备你的第一份合同
开发智能合约