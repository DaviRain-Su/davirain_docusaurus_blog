# 运行时开发

正如[架构](https://docs.substrate.io/fundamentals/architecture/)中所讨论的，Substrate 节点的运行时包含用于执行事务、保存状态转换以及与外部节点交互的所有业务逻辑。Substrate 提供了构建通用区块链组件所需的所有工具，因此您可以专注于开发定义区块链行为的运行时逻辑。

## 状态转换和运行时
在最基本的层面上，每个区块链本质上都是链上发生的每次更改的分类账或记录。在基于 Substrate 的链中，这些状态更改会记录在运行时中。因为运行时处理这个操作，运行时有时被描述为提供[状态转换函数](https://docs.substrate.io/reference/glossary/#state-transition-function-stf)。

因为状态转换发生在运行时，所以运行时是您定义代表区块链[状态](https://docs.substrate.io/reference/glossary/#state)的存储项和允许区块链用户更改此状态的[事务](https://docs.substrate.io/fundamentals/transaction-types/)的地方。


![运行时中的状态和函数](./assets/state-transition-function.avif)
Substrate 运行时确定哪些交易有效和无效，以及如何更改链状态以响应交易。

## 运行时接口

正如您在[架构](https://docs.substrate.io/fundamentals/architecture/)中所了解的，外部节点负责处理对等点发现、事务池、块和事务八卦、共识以及响应来自外部世界的 RPC 调用。这些任务经常需要外部节点向运行时查询信息或向运行时提供信息。运行时 API 促进了外部节点和运行时之间的这种通信。

在 Substrate 中，`sp_api`板条箱提供了一个接口来实现运行时 API。它旨在让您灵活地使用[impl_runtime_apis](https://paritytech.github.io/substrate/master/sp_api/macro.impl_runtime_apis.html) 宏定义自己的自定义接口。但是，每个运行时都必须实现[`Core`](https://paritytech.github.io/substrate/master/sp_api/trait.Core.html)和[`Metadata`](https://paritytech.github.io/substrate/master/sp_api/trait.Metadata.html)接口。除了这些必需的接口之外，大多数 Substrate 节点（如节点模板）还实现了以下运行时接口：

- [BlockBuilder](https://paritytech.github.io/substrate/master/sp_block_builder/trait.BlockBuilder.html)用于构建块所需的功能。
- [TaggedTransactionQueue](https://paritytech.github.io/substrate/master/sp_transaction_pool/runtime_api/trait.TaggedTransactionQueue.html)用于验证交易。
- [OffchainWorkerApi](https://paritytech.github.io/substrate/master/sp_offchain/trait.OffchainWorkerApi.html)用于启用链下操作。
- [AuraApi](https://paritytech.github.io/substrate/master/sp_consensus_aura/trait.AuraApi.html)使用循环的共识方法进行块创作和验证。
- [SessionKeys](https://paritytech.github.io/substrate/master/sp_session/trait.SessionKeys.html)用于生成和解码会话密钥。
- [GrandpaApi](https://paritytech.github.io/substrate/master/sp_finality_grandpa/trait.GrandpaApi.html)用于块最终确定到运行时。
- [AccountNonceApi](https://paritytech.github.io/substrate/master/frame_system_rpc_runtime_api/trait.AccountNonceApi.html)用于查询交易索引。
- [TransactionPaymentApi](https://paritytech.github.io/substrate/master/pallet_transaction_payment_rpc_runtime_api/trait.TransactionPaymentApi.html)用于查询交易信息。
- [Benchmark](https://paritytech.github.io/substrate/master/frame_benchmarking/trait.Benchmark.html)用于估计和测量完成交易所需的执行时间。

## 核心原语
Substrate 还定义了运行时必须实现的核心原语。Substrate 框架对您的运行时必须向 Substrate 的其他层提供什么做出了最小的假设。但是，有一些数据类型必须定义并且必须满足特定的接口才能在 Substrate 框架内工作。

这些核心原语是：

- `Hash`：一种对某些数据的加密摘要进行编码的类型。通常只是一个 256 位的数量。
- `DigestItem`：一种必须能够编码与共识和变更跟踪相关的许多“硬连线”替代方案之一以及与运行时中的特定模块相关的任意数量的“软编码”变体的类型。
- `Digest`: 一系列 DigestItems。这将编码与轻客户端相关的所有信息，以便在块内拥有。
- `Extrinsic`: 表示区块链外部的单个数据的类型，被区块链识别。这通常涉及一个或多个签名，以及某种编码指令（例如，用于转移资金所有权或调用智能合约）。
- `Header`：代表与区块相关的所有信息的类型（加密或其他方式）。它包括父哈希、存储根和外部树根、摘要和块号。
- `Block`：本质上只是`Header`一系列`Extrinsics` 的组合，以及要使用的散列算法的规范。
- `BlockNumber`: 一种对任何有效块具有的祖先总数进行编码的类型。通常为 32 位数量。

## 框架
[FRAME](https://docs.substrate.io/reference/glossary/#frame)是运行时开发人员可用的最强大的工具之一。正如在[Substrate 为开发人员赋能](https://docs.substrate.io/)中所提到的，FRAME 是Framework for Runtime Aggregation of Modularized Entities的首字母缩写，它包含大量可简化运行时开发的模块和支持库。在 Substrate 中，这些模块（称为托盘）为您可能希望包含在运行时中的不同用例和功能提供可定制的业务逻辑。例如，有一些托盘为质押、共识、治理和其他常见活动提供了业务逻辑框架。

有关可用托盘的摘要，请参[阅框架托盘](https://docs.substrate.io/reference/frame-pallets/)。

除了托盘，FRAME 还通过以下库和模块提供与运行时交互的服务

- FRAME 系统 crate [frame_system](https://paritytech.github.io/substrate/master/frame_system/index.html)为运行时提供低级类型、存储和函数。
- FRAME support crate [frame_support](https://paritytech.github.io/substrate/master/frame_support/index.html)是 Rust 宏、类型、特征和模块的集合，可简化 Substrate 托盘的开发。
- FRAME 执行托盘[frame_executive](https://paritytech.github.io/substrate/master/frame_executive/index.html)在运行时协调对各个托盘的传入函数调用的执行。
下图说明了 FRAME 及其系统、支持和执行模块如何为运行时环境提供服务。


![FRAME 和运行时架构](./assets/runtime-and-frame.avif)

## 使用托盘组成运行时
您可以在不使用 FRAME 的情况下构建基于 Substrate 的区块链。但是，FRAME 托盘使您能够使用预定义的组件作为起点来编写自定义运行时逻辑。每个托盘都定义了特定的类型、存储项目和功能，以实现运行时的一组特定特性或功能。

下图说明了如何选择和组合 FRAME 托盘以组成运行时。


![使用 FRAME 编写运行时](./assets/compose-runtime.avif)

## 构建定制托盘
除了预构建 FRAME 托盘库之外，您还可以使用 FRAME 库和服务来构建您自己的自定义托盘。使用自定义托盘，您可以灵活地定义最适合您目的的运行时行为。因为每个托盘都有自己的离散逻辑，所以您可以结合预构建和自定义托盘来控制您的区块链提供的特性和功能，并实现您想要的结果。

例如，您可能在运行时中包含[Balances pallet](https://github.com/paritytech/substrate/tree/master/frame/balances)，以使用其与加密货币相关的存储项和功能来管理令牌，但添加自定义逻辑以在帐户余额发生变化时调用您编写的托盘。

大多数托盘由以下部分的某种组合组成：
- 导入和依赖
- 托盘类型声明
- 运行时配置特征
- 运行时存储
- 运行时事件
- 应在特定上下文中执行的逻辑挂钩
- 可用于执行事务的函数调用

例如，如果您想定义一个自定义pallet，您可以从pallet的骨架结构开始，类似于以下内容：

```rust
// Add required imports and dependencies
pub use pallet::*;
#[frame_support::pallet]
pub mod pallet {
 use frame_support::pallet_prelude::*;
 use frame_system::pallet_prelude::*;

 // Declare the pallet type
 // This is a placeholder to implement traits and methods.
    #[pallet::pallet]
    #[pallet::generate_store(pub(super) trait Store)]
    pub struct Pallet<T>(_);

 // Add the runtime configuration trait
 // All types and constants go here.
 #[pallet::config]
 pub trait Config: frame_system::Config { ... }

 // Add runtime storage to declare storage items.
 #[pallet::storage]
 #[pallet::getter(fn something)]
 pub type MyStorage<T: Config> = StorageValue<_, u32>;

 // Add runtime events
 #[pallet::event]
 #[pallet::generate_deposit(pub(super) fn deposit_event)]
 pub enum Event<T: Config> { ... }

 //  Add hooks to define some logic that should be executed
 //  in a specific context, for example on_initialize.
 #[pallet::hooks]
 impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> { ... }

 // Add functions that are callable from outside the runtime.
 #[pallet::call]
 impl<T:Config> Pallet<T> { ... }

}
```

您可以根据需要将托盘与部分或全部部分组合在一起。当您开始设计和构建自定义运行时，您将了解更多关于 FRAME 库和用于定义配置特征、存储项、事件和错误的运行时原语，以及如何编写分派到运行时的函数调用执行。

## 下一步去哪里

现在您已经熟悉了 Substrate 运行时开发和使用托盘的基础知识，请探索以下主题和教程以了解更多信息。
- [框架托盘](https://docs.substrate.io/reference/frame-pallets/)
- [将模块添加到运行时](https://docs.substrate.io/tutorials/work-with-pallets/add-a-pallet/)
- [基材生锈](https://docs.substrate.io/fundamentals/rust-basics/)
- [宏参考](https://docs.substrate.io/reference/frame-macros/)
- [在自定义托盘中使用宏](https://docs.substrate.io/tutorials/work-with-pallets/use-macros-in-a-custom-pallet/)