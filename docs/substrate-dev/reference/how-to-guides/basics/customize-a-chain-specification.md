# 自定义链规范
制作好 Substrate 节点后，您想启动一个有许多对等点的网络！本指南展示了一种统一创建链规范文件并分发它们的方法，以便其他节点可以明确地发现并与您的网络对等。

本指南说明：

如何生成chain-spec.json并包含它以供其他节点加入公共网络
如何在不编辑节点源代码的情况下修改现有的普通链规范
创建链规范
普通链规格文件

从节点的工作目录开始，使用以下命令生成一个普通的链规范：

./target/release/node-template build-spec > chain-spec-plain.json

我们刚刚为您的文件中设置的默认网络 生成了一个普通的链规范文件。该文件可以传递给其他节点。chain_spec.rs

修改普通链规范（可选）：

这个可选步骤我们可以利用现有的普通链规范用于网络，否则需要修改节点源才能在新网络上运行。例如，这在准备本地平行链测试网中非常有用，我们希望在不自定义 Polkadot 源的情况下创建自定义中继链。

这里我们使用相同的链规范，但传递一个标志来禁用引导节点，因为我们想要一个这些节点不同的新网络。

./target/release/node-template build-spec --chain chain-spec-plain.json --raw --disable-default-bootnode > no-bootnodes-chain-spec-plain.json

这no-bootnodes-chain-spec-plain.json可用于生成 SCALE 存储编码、可分发的原始链规范。

原始链规格文件

生成原始链规范。

有了一个普通的规范，你可以通过运行生成最终的原始链规范：

./target/release/node-template build-spec --chain chain-spec-plain.json --raw > chain-spec.json

当传递到节点以执行它时，应始终使用原始链规范。

发布原始链规范
因为针对 WebAssembly 的 Rust 构建已经过优化，所以二进制文件不能确定地重现。如果每个网络参与者都生成普通和/或原始链规范，则生成的 Wasm blob 中的差异将破坏共识。

传统的做法是在源代码本身中包含节点的链规范文件，这样任何人都可以以相同的方式构建您的节点，从而通过比较创世 blob 与另一个来轻松检查不确定性。在源代码中可以找到 Polkadot、Kusama、Rococo 和更多网络链规范文件以及一个.gitignore文件，以确保您在!/*.json进一步构建节点软件并进行运行时升级时不会意外更改这些文件。

启动一个新节点
如果您发布一个节点二进制文件，或者让用户自己构建，然后他们想加入您的网络，那么只需要相同的原始链规范文件并运行您的二进制文件：

# binary named `node-template`
# `chain-spec.json` obtained from canonical common source
node-template --chain chain-spec.json

这也可以简单地配置为默认网络。作为参考，您可以在此处查看Polkadot 如何实现使用源代码中各种网络的链规范的默认命令。

例子
添加可信节点
类似 Polkadot 的网络链规格
用于许多网络的 Polkadot 命令