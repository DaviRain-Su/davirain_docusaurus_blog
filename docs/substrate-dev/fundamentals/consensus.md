# 共识

所有区块链都需要某种类型的共识机制来就区块链状态达成一致。由于 Substrate 为构建区块链提供了模块化框架，因此它支持几种不同的模型让节点达成共识。一般来说，不同的共识模型有不同的权衡取舍，所以选择你想为你的链使用的共识类型是一个重要的考虑因素。Substrate 默认支持的共识模型需要最少的配置，但如果需要，也可以构建自定义共识模型。

## 共识分两个阶段
与某些区块链不同，Substrate 将达成共识的要求分为两个独立的阶段：

- 块创作是节点用于创建新块的过程。
- 区块终结是用于处理分叉和选择规范链的过程。

### 块创作
在达成共识之前，区块链网络中的一些节点必须能够产生新的区块。区块链如何决定授权创建区块的节点取决于您使用的共识模型。例如，在集中式网络中，单个节点可能会编写所有块。在没有任何可信节点的完全去中心化网络中，算法必须在每个区块高度选择区块作者。

对于基于 Substrate 的区块链，您可以选择以下区块创作算法之一或创建您自己的算法：

基于权限的循环调度 ( [Aura](https://docs.substrate.io/reference/glossary/#authority-round-aura) )。
盲分配区块链扩展（[BABE](https://docs.substrate.io/reference/glossary/#blind-assignment-of-blockchain-extension-babe)）基于槽的调度。
基于工作证明计算的调度。
Aura 和 BABE 共识模型要求您拥有一组已知的允许生成块的验证器节点。在这两种共识模型中，时间都被划分为离散的时隙。在每个插槽中，只有一些验证者可以生成一个块。在 Aura 共识模型中，可以创建区块的验证者以循环方式轮换。在 BABE 共识模型中，验证者是基于可验证随机函数 (VRF) 选择的，而不是循环选择方法。

在工作量证明共识模型中，如果节点解决了计算密集型问题，任何节点都可以随时生成一个块。解决该问题需要 CPU 时间，因此节点只能按其计算资源的比例生成块。Substrate 提供了一个工作量证明区块生产引擎。

### 完成和分叉
作为一个原语，一个块包含一个 header 和[transactions](https://docs.substrate.io/fundamentals/transaction-types/)。每个块头都包含对其父块的引用，因此您可以将链追溯到其起源。当两个块引用同一个父块时，就会发生分叉。区块终结是一种解决分叉的机制，使得只存在规范链。

分叉选择规则是一种算法，它选择应该扩展的最佳链。Substrate 通过[SelectChaintrait](https://paritytech.github.io/substrate/master/sp_consensus/trait.SelectChain.html) 暴露了这个分叉选择规则。你可以使用 trait 来编写你的自定义分叉选择规则，或者使用[GRANDPA](https://github.com/w3f/consensus/blob/master/pdf/grandpa.pdf)，Polkadot 和类似链中使用的最终机制。

在 GRANDPA 协议中，最长链规则简单地说最好的链就是最长的链。Substrate 为结构提供了这个链选择规则[LongestChain](https://paritytech.github.io/substrate/master/sc_consensus/struct.LongestChain.html)。GRANDPA 在其投票机制中使用最长链规则。


![最长链式法则](./assets/consensus-longest-chain.avif)
Greedy Heaviest Observed SubTree (GHOST) 规则说，从创世区块开始，每个分叉都是通过递归选择构建在其上的区块最多的分支来解决的。


![GHOST规则](./assets/consensus-ghost.avif)

## 确定性终结
用户很自然地想知道交易何时完成并由某些事件发出信号，例如交付收据或签署文件。然而，根据迄今为止描述的块创作和分叉选择规则，交易永远不会完全完成。更长或更重的链总是有可能恢复交易。但是，在特定块之上构建的块越多，它被还原的可能性就越小。通过这种方式，块创作以及适当的分叉选择规则提供了概率确定性。

如果您的区块链需要确定性的确定性，您可以在区块链逻辑中添加确定性机制。例如，您可以让固定权限集的成员投票。当某个区块获得足够多的选票时，该区块被视为最终区块。在大多数区块链中，这个阈值是三分之二。如果没有外部协调（例如硬分叉），则无法恢复已完成的块。

在某些共识模型中，区块生产和区块最终确定性是结合在一起的，在N+1区块最终确定之前无法创作新区块N。如您所见，在 Substrate 中，这两个进程是相互隔离的。通过将区块创作与区块最终化分开，Substrate 使您能够使用任何具有概率最终性的区块创作算法，或将其与最终性机制结合以实现确定性最终性。

如果您的区块链使用确定性机制，您必须修改分叉选择规则以考虑确定性投票的结果。例如，节点不会采用最长的链周期，而是采用包含最近完成的区块的最长链。

## 默认共识模型
尽管您可以实现自己的共识机制，但[Substrate 节点模板](https://github.com/substrate-developer-hub/substrate-node-template)默认包含用于块创作和 GRANDPA 最终确定的 Aura。Substrate 还提供了 BABE 和工作量证明共识模型的实现。

### Aura
[Aura](https://paritytech.github.io/substrate/master/sc_consensus_aura/index.html)提供了一种基于插槽的块创作机制。在 Aura 中，一组已知的权威轮流生产区块。

### Babe

[BABE](https://paritytech.github.io/substrate/master/sc_consensus_babe/index.html)通过一组已知的验证器提供基于插槽的块创作，通常用于权益证明区块链。与 Aura 不同，插槽分配基于可验证随机函数 (VRF) 的评估。每个验证者都被分配了一个epoch 的权重。 这个 epoch 被分成多个槽，验证器在每个槽评估它的 VRF。对于验证者的 VRF 输出低于其权重的每个槽，允许创建一个块。

因为多个验证者可能能够在同一个时段产生一个块，所以即使在良好的网络条件下，分叉在 BABE 中也比在 Aura 中更常见。

Substrate 对 BABE 的实现也有一个备用机制，用于在给定插槽中未选择任何权限时。这些辅助槽分配允许 BABE 实现恒定的出块时间。

### 工作证明
[工作量证明](https://paritytech.github.io/substrate/master/sc_consensus_pow/index.html)块创作不是基于槽的，并且不需要已知的权限集。在工作量证明中，任何人都可以随时生成一个块，只要他们能够解决计算上具有挑战性的问题（通常是哈希原像搜索）。可以调整此问题的难度以提供统计目标块时间。

### grandpa

[GRANDPA](https://paritytech.github.io/substrate/master/sc_finality_grandpa/index.html)提供区块最终确定。它有一个已知的加权权限集，如 BABE。但是，GRANDPA 不创作区块。它只是听取关于由块创作节点产生的块的八卦。GRANDPA 验证者对链进行投票，而不是对区块进行投票。GRANDPA 验证者对他们认为最佳的区块进行投票，并且他们的投票可传递地应用于所有先前的区块。在三分之二的 GRANDPA 当局投票支持特定区块后，该区块被视为最终区块。

所有确定性终结性算法，包括 GRANDPA，都至少需要2f + 1非故障节点，其中f是故障或恶意节点的数量。在[开创性论文《在存在故障的情况下达成共识》](https://lamport.azurewebsites.net/pubs/reaching.pdf)或[维基百科：拜占庭故障](https://en.wikipedia.org/wiki/Byzantine_fault)中了解有关此阈值的来源以及为什么它是理想的更多信息。

并非所有共识协议都定义一个单一的规范链。当具有相同父级的两个块没有冲突的状态更改时，一些协议会验证[有向无环图(DAG)](https://en.wikipedia.org/wiki/Directed_acyclic_graph)。有关此类示例，请参见[AlephBFT](https://github.com/aleph-zero-foundation/aleph-node) 。

## 下一步去哪里

- [Babe研究](https://research.web3.foundation/en/latest/polkadot/block-production/Babe.html)
- [grandpa研究](https://research.web3.foundation/en/latest/polkadot/finality.html)