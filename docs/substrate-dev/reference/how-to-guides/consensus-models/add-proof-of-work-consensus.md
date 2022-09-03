# 配置链以使用工作量证明

该basic-pow节点演示了如何将自定义共识引擎添加到基于 Substrate 的区块链中。在此示例中，节点使用最小的工作量证明共识引擎在区块链上达成一致。本指南介绍了使用共识引擎的一些核心原则。

用例
启动使用工作量证明共识引擎的链。
将链从基于权威的共识引擎升级为工作量证明共识引擎。
步骤预览
定义一个使用工作量证明共识的完整节点。
为数据提供者创建导入队列。
定义proposer和worker函数。
定义轻客户端服务。
使用 sc共识pow 和 sc_service定义一个完整节点
在src/service.rs中，创建一个new_full1定义PartialComponents和的函数PowBlockImport：

let pow_block_import = sc_consensus_pow::PowBlockImport::new(
  client.clone(),
  client.clone(),
  sha3pow::MinimalSha3Algorithm,
  0,                              // check inherents starting at block 0
  select_chain.clone(),
  inherent_data_providers.clone(),
  can_author_with,
);

let import_queue = sc_consensus_pow::import_queue(
  Box::new(pow_block_import.clone()),
  None,
  sha3pow::MinimalSha3Algorithm,  // provide it with references to our client
  inherent_data_providers.clone(),
  &task_manager.spawn_handle(),
  config.prometheus_registry(),
)?;

创建导入队列
InherentDataProviders通过在定义 POW 系统提供者的函数中使用来定义节点的固有特性：

pub fn build_inherent_data_providers() -> Result<InherentDataProviders, ServiceError> {
  let providers = InherentDataProviders::new();

  providers
    .register_provider(sp_timestamp::InherentDataProvider)
    .map_err(Into::into)
    .map_err(sp_consensus::error::Error::InherentData)?;

    Ok(providers)
}

定义提议者和工作者
在new_full函数中，定义proposer：

let proposer = sc_basic_authorship::ProposerFactory::new(
    task_manager.spawn_handle(),
    client.clone(),
    transaction_pool,
    prometheus_registry.as_ref(),
);

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

让任务管理器生成它：

task_manager
    .spawn_essential_handle()
    .spawn_blocking("pow", worker_task);

例子
基本 POW 示例节点
资源
部分组件
Pow 块导入
创建固有的数据提供者
战俘算法