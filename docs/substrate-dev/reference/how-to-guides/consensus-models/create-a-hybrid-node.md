# 创建混合节点

本指南演示了如何创建一个采用混合共识的基于 Substrate 的节点，使用 SHA3 工作证明来指示块创作，并使用Grandpa finality小工具来提供确定性的确定性。最小的工作证明共识完全存在于运行时之外。

Grandpa 终结性依赖于使用Grandpa API从运行时获取其权限集。因此，您需要一个提供此 API 的运行时来成功编译实现本指南的节点。

用例
自定义 Substrate 链的共识机制。

步骤预览
配置块导入管道。
创建导入队列。
产生工作证明作者身份任务。
产生爷爷任务。
配置块导入管道
我们首先为 Grandpa 创建块导入。除了块导入本身，我们还得到一个grandpa_link. 该链接是一个通道，块导入可以通过该通道与实际投票的后台任务进行通信。Grandpa 协议的详细信息超出了本指南的范围。

在node/src/service.rs中，创建 Grandpa 块导入：

let (grandpa_block_import, grandpa_link) = sc_finality_grandpa::block_import(
	client.clone(),
	&(client.clone() as std::sync::Arc<_>),
	select_chain.clone(),
)?;

创建了爷爷块导入后，我们现在可以创建 PoW 块导入。Pow 块导入是块导入洋葱的最外层，它包裹了爷爷块导入。

let pow_block_import = sc_consensus_pow::PowBlockImport::new(
	grandpa_block_import,
	client.clone(),
	sha3pow::MinimalSha3Algorithm,
	0, // check inherents starting at block 0
	select_chain.clone(),
	inherent_data_providers.clone(),
	can_author_with,
);

创建导入队列
设置好块导入后，我们可以继续创建导入队列。我们使用 PoW 的import_queue辅助函数来实现。请注意，它需要整个区块导入管道，我们称之为pow_block_import因为 PoW 是最外层。

let import_queue = sc_consensus_pow::import_queue(
	Box::new(pow_block_import.clone()),
	None,
	sha3pow::MinimalSha3Algorithm,
	inherent_data_providers.clone(),
	&task_manager.spawn_handle(),
	config.prometheus_registry(),
)?;

产生 PoW 作者身份任务
任何充当权威的节点，在 PoW 上下文中通常称为“矿工”，都必须运行由任务管理器生成的采矿工人。

let (_worker, worker_task) = sc_consensus_pow::start_mining_worker(
	Box::new(pow_block_import),
	client,
	select_chain,
	MinimalSha3Algorithm,
	proposer,
	network.clone(),
	None,
	inherent_data_providers,
	// time to wait for a new block before starting to mine a new one
	Duration::from_secs(10),
	// how long to take to actually build the block (i.e. executing extrinsics)
	Duration::from_secs(10),
	can_author_with,
);

task_manager
	.spawn_essential_handle()
	.spawn_blocking("pow", worker_task);

产生爷爷任务
爷爷不是CPU 密集型的，所以我们使用一个标准的async工人来听爷爷投票。我们首先创建一个 Grandpa Config：

let grandpa_config = sc_finality_grandpa::Config {
	gossip_duration: Duration::from_millis(333),
	justification_period: 512,
	name: None,
	observer_enabled: false,
	keystore: Some(keystore_container.sync_keystore()),
	is_authority,
};

然后我们可以使用这个配置来创建一个GrandpaParams.

let grandpa_config = sc_finality_grandpa::GrandpaParams {
	config: grandpa_config,
	link: grandpa_link,
	network,
	telemetry_on_connect: telemetry_connection_notifier.map(|x| x.on_connect_stream()),
	voting_rule: sc_finality_grandpa::VotingRulesBuilder::default().build(),
	prometheus_registry,
	shared_voter_state: sc_finality_grandpa::SharedVoterState::empty(),
};

建立好参数后，我们现在可以创建和产生作者身份的未来。

task_manager.spawn_essential_handle().spawn_blocking(
	"grandpa-voter",
	sc_finality_grandpa::run_grandpa_voter(grandpa_config)?
);

例子
混合共识
资源
POW 算法特征
PowBlockimport
固有的
