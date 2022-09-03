# 框架托盘

FRAME 开发环境提供模块（称为托盘）和支持库，您可以使用、修改和扩展这些库来构建运行时逻辑以满足区块链的需求。

本节概述了预定义的托盘和指向 Rust API 参考文档的链接，您可以在其中找到有关每个托盘接口的详细信息。

## 系统托盘
FRAME 系统托盘是 Substrate 运行时不可或缺的一部分，并提供所有其他托盘所依赖的核心功能。

系统托盘名称	这是为了什么
- frame_executive	通过将传入的函数调用发送到运行时中的适当托盘来编排它们。
- frame_support	提供 Rust 宏、类型、特征和模块，在编译时为托盘结构生成样板代码。
- frame_system	为区块链的 Substrate 原语、存储项和核心功能定义低级类型。所有其他托盘取决于frame_system板条箱。

## 功能托盘
Substrate 开发框架包括许多功能托盘，这些托盘提供您可能会发现通常用作区块链的可组合组件的功能。这些功能托盘是预先构建的并且可以免费使用，以使社区能够共享、重用和改进接口以解决常见用例。

下表重点介绍了一些最常用的可用托盘。但是，提供专门功能的托盘数量以及它们提供该功能的方式经常发生变化。您应该查看Rust 文档以获取最新的托盘列表以及有关使用它们的详细信息。

预建托盘名称	这是为了什么
- pallet_assets	为处理可替代资产提供简单而安全的方法。
- pallet_atomic_swap	允许将资金从来源发送到目标。证明用于允许目标要求交换。如果在指定的时间内没有要求交换，发件人可以取消它。
- pallet_aura	通过管理离线报告扩展权威轮（Aura）共识模型。
- pallet_authority_discovery	检索当前的一组权威，学习自己的权威 ID，并签署和验证与其他权威之间的消息。
- pallet_authorship	跟踪区块的当前作者和最近的叔叔。
- pallet_babe	通过从 VRF 输出收集链上随机性和管理 epoch 转换来扩展 BABE 共识。
托盘余额	提供处理帐户和余额的功能。
- frame_benchmarking	包含用于基准测试和测试目的的常见运行时模式。
- pallet_collective	允许一组帐户 ID 通过来自特定来源的调度呼叫来表达他们的集体感受。
- pallet_contracts	为运行时提供部署和执行 WebAssembly 智能合约的功能。
- pallet_democracy	提供处理一般利益相关者投票管理的民主系统。
- pallet_election_provider_multi_phase	使选举提供者能够进行由签名和未签名阶段组成的选举。
- pallet_elections_phragmen	提供基于顺序 Phragmén的选举模块。
- pallet_example_basic	展示适用于大多数托盘的概念、API 和结构。
- pallet_example_offchain_worker	展示适用于大多数链下工作者的概念、API 和结构。
- pallet_example_parallel	演示对入伍参与者的并行验证。
- pallet_grandpa	通过管理为本地代码准备的 GRANDPA 权限集来扩展 GRANDPA 共识。
- pallet_identity	启用允许从指定来源添加多个注册商的联合命名系统。注册服务商可以设置费用来提供身份验证服务。
- pallet_im_online	允许验证者在每个新会话中发送心跳事务，以表明节点在线。
- pallet_indices	为新创建的帐户分配索引。索引是地址的缩写形式。
- pallet_membership	允许控制一组AccountIds 的成员资格，这对于管理集体的成员资格很有用。
- pallet_multisig	启用多重签名调度。
- pallet_nicks	演示简化的链上账户命名。它无需努力创建名称层次结构、替代 DNS 或提供反向查找。
- pallet_offences	跟踪报告的罪行。
- pallet_proxy	允许帐户授予其他帐户从其签名来源发送呼叫类型的权限。
- pallet_randomness_collective_flip	提供一个random可以在测试中使用的函数，并根据之前81区块的区块哈希生成低影响的随机值。此托盘不适用于生产。
- pallet_recovery	为用户提供社交恢复工具，以在其私钥或其他身份验证机制丢失时访问其帐户。此托盘使帐户所有者能够识别可以代表所有者采取行动以恢复对帐户的访问权限的受信任方。
- pallet_scheduler	公开调度调度以在指定的块号或指定的时间段发生的能力。这些计划调度可以命名或匿名，并且可以取消。
- pallet_scored_pool	维护一个评分会员池，其中评分最高的实体成为会员。
- pallet_session	允许验证者管理他们的会话密钥，提供更改会话长度的功能，并处理会话轮换。
- pallet_society	为用户参与和维护会员制社会提供经济激励。
- pallet_staking	管理由网络维护者质押的资金。
- pallet_sudo	允许单个帐户（称为 sudo 键）执行需要Root来源或指定新帐户以将其替换为 sudo 键的可调度功能。
- pallet_timestamp	提供获取和设置链上时间的功能。
- pallet_transaction_payment	提供计算发货前交易费用的基本逻辑。
- pallet_treasury	提供可由系统中的利益相关者管理的资金储备，以及从该储备中提出支出建议的结构。
- pallet_utility	提供用于管理调度的无状态助手模块。
- pallet_vesting	在账户的锁定余额上放置一条线性曲线。该模块确保存在锁定，以防止余额因交易费支付以外的任何原因跌至未归属金额以下。

## 平行链托盘
除了通常对任何区块链有用的功能性托盘外，还有一些预构建的托盘，专门为旨在连接到中继链的区块链提供功能。以下托盘为平行链开发提供了功能。

预建托盘名称	这是为了什么
- cumulus-pallet-aura-ext	为平行链提供 AURA 共识。
- pallet-collator-selection	管理平行链中的收集人。
- cumulus-pallet-dmp-queue	实现一个消息队列，用于接收来自中继链的消息。
- cumulus-pallet-parachain-system	为基于积云的平行链提供基本功能。
- cumulus-pallet-solo-to-para	允许从单链迁移到平行链。
- cumulus-pallet-xcm	添加对平行链的跨链消息传递 (XCMP) 的支持。
- cumulus-pallet-xcmp-queue	使 XCMP 传输层能够处理传入和传出的消息发送和分派、排队、信令和背压。

## 附加信息

有关任何托盘的详细信息，请参阅Rust 生成的 API文档或单个托盘的源代码。