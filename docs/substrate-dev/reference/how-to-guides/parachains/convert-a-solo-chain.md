# 转换一个单独的链
本指南将向您展示如何将 Cumulus 添加到单链并使用中继链提供最终确定性。

你将学到如何：

引导单链的安全性
使用公共中继链上的平行链访问 XCMP
Cumulus 与任何 Substrate 链的集成使该链能够将其最终性与中继链（如 Polkadot）耦合起来。本指南不介绍如何迁移正在运行的单链，仅说明将节点的代码库转换为使用 Cumulus 达成共识所需的步骤，而不是像其他 Substrate 单链常见的 GRANDPA 之类的东西。

这是一个概述，目前不是正确的操作指南！

平行链节点模板概述
熟悉 Substrate 节点模板的 Substrate 开发者会发现 Substrate 平行链模板很熟悉。它们具有相同的一般结构，包括node、runtime和pallets目录。它们的运行时间相似，并且具有许多相同的托盘。除了一些新特性之外，它pallet-template本身基本上是相同的。许多教程只需对平行链模板进行少量修改即可使用。

这两个模板之间的相似之处应该让您相信，如果您已经构建了 Substrate 链，那么构建平行链将毫无问题！

与节点模板的区别
但是，这两个模板之间存在一些重要的差异，一开始就值得观察。

平行链信息托盘
平行链模板运行时（runtime/Cargo.toml）已集成parachain-info托盘。该托盘旨在将有关平行链注册的信息注入其自己的运行时。目前它只是注入ParaID链注册的那个。这允许运行时知道哪些跨链消息是针对它的。

register_validate_block!宏
每个平行validate_block链在注册时必须向中继链提供一个函数，表示为 Wasm blob。节点模板不提供此功能，但平行链模板提供，感谢 cumulus，为 Substrate 运行时创建此功能就像添加一行代码一样简单，如运行时底部所示：

cumulus_pallet_parachain_system::register_validate_block!(
  Runtime = Runtime,
  BlockExecutor = cumulus_pallet_aura_ext::BlockExecutor::<Runtime, Executive>,
  CheckInherents = CheckInherents,
);

无GRANDPA托盘
许多流行的 Substrate 运行时（包括节点模板）都具有与最终性相关的 GRANDPA 托盘及其相关的GrandpaApi. 平行链模板中都缺少这些。

这是因为平行链遵循中继链的终结性，而不是运行自己的终结性小工具。这是 Polkadot 架构的基础，不会改变。

服务
整理者服务 ( node/src/service.rs) 与 Node 模板完全不同。虽然您可以找到相似之处，但服务的结构却大不相同。这项新服务是 cumulus 提供的主要变化。

在修改现有的 Substrate 链以使用 Cumulus 时，通常最好从平行链模板中复制服务代码。