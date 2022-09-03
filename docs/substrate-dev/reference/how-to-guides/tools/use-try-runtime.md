# 使用尝试运行时
该try-runtime工具使您能够在将运行时投入生产之前在模拟环境中运行测试和验证操作。本指南演示了将该try-runtime工具集成到运行时的基本步骤，以便您可以使用它来测试存储迁移。

通常，将try-runtime工具添加到运行时类似于导入托盘。它涉及在正确的位置添加适当的依赖项并更新运行时逻辑以包含这些try-runtime功能。try-runtime与托盘一样，在向运行时添加依赖项时，请确保使用工具的适当标签或分支。

添加运行时依赖项
打开终端外壳并切换到节点模板的根目录。
runtime/Cargo.toml在文本编辑器中打开配置文件。
找到 [dependencies] 部分并注意如何导入其他托盘。
添加frame-try-runtime依赖：
```
[dependencies]
 frame-try-runtime = { git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26", optional = true }
```
添加try-runtime-cli依赖：
```
try-runtime-cli = { git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26", optional = true }
```
添加frame-try-runtime到标准功能列表：
```
	[features]
	default = ["std"]
	std = [
		"codec/std",
		"scale-info/std",
		"frame-try-runtime/std",
		...
	]
	```
```
在包含运行时中的每个托盘try-runtime的部分中添加或更新。[features]
```
try-runtime = [
	 "frame-executive/try-runtime",
	 "frame-try-runtime",
	 "frame-system/try-runtime",
	 "pallet-aura/try-runtime",
	 "pallet-balances/try-runtime",
	 "pallet-nicks/try-runtime",
	 "pallet-grandpa/try-runtime",
	 "pallet-randomness-collective-flip/try-runtime",
	 "pallet-sudo/try-runtime",
	 "pallet-template/try-runtime",
	 "pallet-timestamp/try-runtime",
	 "pallet-transaction-payment/try-runtime",
	]
```
在运行时 api 中实现 try-runtime
为 try-runtime 功能添加一个配置块。
```
// Implementation of runtime's apis
impl_runtime_apis! {
...
	/* --snip-- */
#[cfg(feature = "try-runtime")]
impl frame_try_runtime::TryRuntime<Block> for Runtime {
   fn on_runtime_upgrade() -> (frame_support::weights::Weight, frame_support::weights::Weight) {
       log::info!("try-runtime::on_runtime_upgrade.");
       // NOTE: intentional unwrap: we don't want to propagate the error backwards, and want to
       // have a backtrace here. If any of the pre/post migration checks fail, we shall stop
       // right here and right now.
       let weight = Executive::try_runtime_upgrade().map_err(|err|{
           log::info!("try-runtime::on_runtime_upgrade failed with: {:?}", err);
           err
       }).unwrap();
       (weight, RuntimeBlockWeights::get().max_block)
   }
   fn execute_block_no_check(block: Block) -> frame_support::weights::Weight {
       Executive::execute_block_no_check(block)
   }
}
	```
```
添加节点依赖
node/Cargo.toml在文本编辑器中打开配置文件。
找到 [dependencies] 部分并注意如何导入其他托盘。
添加frame-try-runtime依赖：
```
frame-try-runtime = { git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26", optional = true }
```
添加try-runtime-cli依赖：
```
try-runtime-cli = { git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.26", optional = true }
```
添加或更新cli和部分try-runtime。[features]
```
[features]
...
cli = [ "try-runtime-cli" ]
try-runtime = [ "node-template-runtime/try-runtime", "try-runtime-cli" ]
...
```
添加命令行子命令
node/src/cli.rs在文本编辑器中打开文件。
```
/* --snip-- */
/// Try some command against runtime state.
#[cfg(feature = "try-runtime")]
TryRuntime(try_runtime_cli::TryRuntimeCmd),

/// Try some command against runtime state. Note: `try-runtime` feature must be enabled.
#[cfg(not(feature = "try-runtime"))]
TryRuntime,
/* --snip-- */
```
添加命令
node/src/commands.rs在文本编辑器中打开文件。
添加命令
```
/* --snip-- */
#[cfg(feature = "try-runtime")]
Some(Subcommand::TryRuntime(cmd)) => {
	let runner = cli.create_runner(cmd)?;
	runner.async_run(|config| {
		// only need a runtime or a task manager to do `async_run`.
		let registry = config.prometheus_config.as_ref().map(|cfg| &cfg.registry)let task_manager = sc_service::TaskManager::new(
		  config.tokio_handle.clone(),
			registry,
		).map_err(|e| sc_cli::Error::Service(sc_service::Error::Prometheus(e)))?;
		
	  Ok((cmd.run::<Block, service::ExecutorDispatch>(config), task_manager))
	})
},

#[cfg(not(feature = "try-runtime"))]
Some(Subcommand::TryRuntime) => {
	Err("TryRuntime wasn't enabled when building the node. \
	You can enable it with `--features try-runtime`.".into())
	},
/* --snip-- */
```
如果您在工作区中使用自定义托盘，请确保您包含try-runtime在工作区文件中的依赖pallets/pallet_name/Cargo.toml项中。

使用尝试运行时
使用 try-runtime 工具类似于编写单元测试。

要使用try-runtime：

创建一个外部性实例。
调用execute_with实例。
下一步去哪里
命令行参考：try-runtime
TryRuntime API
