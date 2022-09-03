# 尝试运行时

该try-runtime工具用于查询运行时存储的快照，使用内存中的外部性来存储状态。通过使用内存存储，您可以为指定的运行时状态编写测试，使您能够在投入生产之前针对真实的链状态进行测试。使用try-runtime命令行界面，您可以指定要查询的块。

以最简单的形式，try-runtime它是一种工具，使您能够：

连接到远程节点。
调用一些运行时 API。
从给定块的节点检索状态。
为检索到的数据编写测试。
动机
最初的动机try-runtime来自于需要针对来自真实链的状态测试运行时更改。以前TestExternalities并BasicExternalities存在用于使用模拟数据编写单元和集成测试，但缺乏针对链的实际状态进行测试的能力。该工具使用节点的以下 RPC 端点try-runtime扩展TestExternalities并检索状态：BasicExternalities

get_storage
get_keys_paged
在使用键值数据库检索状态后，try-runtime 将数据插入到TestExternalities.

这个怎么运作
该try-runtime工具有自己的外部性实现，称为remote_externalities它只是一个TestExternalities使用通用键值存储的包装器，其中数据是类型编码的。

下图说明了外部性位于已编译运行时之外的方式，作为捕获该运行时存储的一种方式。


存储外部性
使用remote_externalities，您可以捕获一些链状态并在其上运行测试。本质上，将使用真实链的数据RemoteExternalities填充 a 。TestExternalities


外部性测试
要查询状态，try-runtime请使用 提供的 RPC 方法StateApiClient。尤其是：

storage 此方法返回表示您指定的块的键的存储值。
storage_key_paged 此方法返回与您指定的带有分页支持的前缀匹配的键。
用法
最常见的用例try-runtime是帮助您准备存储迁移和运行时升级。

因为查询存储的 RPC 调用计算量很大，所以在使用try-runtime命令之前，您应该为正在运行的节点设置许多命令行选项。要准备节点进行try-runtime测试，请设置以下选项：

设置--rpc-max-payload 1000以确保大型 RPC 查询可以工作。
设置--rpc-cors all以确保 WebSocket 连接可以通过。
您可以在生产前结合try-runtime使用fork-off-substrate来测试您的链条。用于try-runtime测试您的链的迁移及其前后状态。然后，fork-off-substrate如果要检查迁移后块生产是否继续，请使用。

运行时升级挂钩
默认情况下，运行时升级挂钩（可以在运行时内部或托盘内部定义）指定在运行时升级时应该发生什么。即默认on_runtime_upgrade方法只描述升级后的运行时状态。但是，可以使用 提供的方法try-runtime来检查和比较运行时升级前后的运行时状态以进行测试。

如果try-runtime为运行时启用该功能，则可以为运行时定义pre-upgrade和post-upgrade挂钩，如下所示：
```
#[cfg(feature = "try-runtime")]
fn pre_upgrade() -> Result<(), &'static str> { Ok(()) }

#[cfg(feature = "try-runtime")]
fn post_upgrade() -> Result<(), &'static str> { Ok(()) }
```
除了pre_upgrade和post_upgrade方法之外，OnRuntimeUpgradeHelpersExt还提供了一组try-runtime用于测试存储迁移的辅助函数。这些辅助函数包括以下内容：

storage_key：生成此运行时升级唯一的存储密钥。这可用于将数据从升级前状态传递到升级后状态并检查它们。
set_temp_storage将一些临时数据写入可以读取的特定存储（可能在升级后挂钩中）。
get_temp_storage: 获取由 . 写入的临时存储数据set_temp_storage。
使用该frame_executive::Executive结构，这些辅助函数将如下所示：
```
pub struct CheckTotalIssuance;
impl OnRuntimeUpgrade for CheckTotalIssuance {
	#[cfg(feature = "try-runtime")]
	fn post_upgrade() {
		// iterate over all accounts, sum their balance and ensure that sum is correct.
	}
}

pub struct EnsureAccountsWontDie;
impl OnRuntimeUpgrade for EnsureAccountsWontDie {
	#[cfg(feature = "try-runtime")]
	fn pre_upgrade() {
		let account_count = frame_system::Accounts::<Runtime>::iter().count();
		Self::set_temp_storage(account_count, "account_count");
	}

	#[cfg(feature = "try-runtime")]
	fn post_upgrade() {
		// ensure that this migration doesn't kill any account.
		let post_migration = frame_system::Accounts::<Runtime>::iter().count();
		let pre_migration = Self::get_temp_storage::<u32>("account_count");
		ensure!(post_migration == pre_migration, "error ...");
	}
}

pub type CheckerMigrations = (EnsureAccountsWontDie, CheckTotalIssuance);
pub type Executive = Executive<_, _, _, _, (CheckerMigrations)>;
```
命令行示例
要try-runtime从命令行使用，请使用--features=try-runtime标志运行您的节点。例如：
```
cargo run --release --features=try-runtime try-runtime
```
您可以使用以下子命令try-runtime：

on-runtime-upgrade：tryRuntime_on_runtime_upgrade针对给定的运行时状态执行。
offchain-worker：offchainWorkerApi_offchain_worker针对给定的运行时状态执行。
execute-block：core_execute_block使用给定块和父块的运行时状态执行。
follow-chain：遵循给定链的最终块并应用于其所有外部。这允许在很长一段时间内检查新运行时的行为，并将真实事务作为输入。
要查看特定try-runtime子命令的使用信息，请指定子命令和--help标志。例如，要查看 的使用信息try-runtime on-runtime-upgrade，您可以运行以下命令：
```
cargo run --release --features=try-runtime try-runtime on-runtime-upgrade --help
```
例如，您可以try-runtime使用以下on-runtime-upgrade命令运行在本地运行的链的子命令：
```
cargo run --release --features=try-runtime try-runtime on-runtime-upgrade live ws://localhost:9944
```
您可以使用以下命令从链下工作人员try-runtime重新执行代码：ElectionProviderMultiPhaselocalhost:9944
```
cargo run -- --release \
   --features=try-runtime \
   try-runtime \
   --execution Wasm \
   --wasm-execution Compiled \
   offchain-worker \
   --header-at 0x491d09f313c707b5096650d76600f063b09835fd820e2916d3f8b0f5b45bec30 \
   live \
   -b 0x491d09f313c707b5096650d76600f063b09835fd820e2916d3f8b0f5b45bec30 \
   -m ElectionProviderMultiPhase \
   --uri wss://localhost:9944
```
您可以使用以下命令在 SomeChain 的状态上运行本地运行时的迁移：
```
RUST_LOG=runtime=trace,try-runtime::cli=trace,executor=trace \
   cargo run try-runtime \
   --execution Native \
   --chain somechain-dev \
   on-runtime-upgrade \
   live \
   --uri wss://rpc.polkadot.io
```
您可以使用如下命令针对特定块号的状态运行 try-runtime：
```
RUST_LOG=runtime=trace,try-runtime::cli=trace,executor=trace \
   cargo run try-runtime \
   --execution Native \
   --chain dev \
   --no-spec-name-check \
   on-runtime-upgrade \
   live \
   --uri wss://rpc.polkadot.io \
   --at <block-hash>
```
请注意，此命令需要--no-spec-name-check命令行选项。

下一步去哪里
存储密钥
OnRuntimeUpgrade
try-runtime-upgrade
放样托盘