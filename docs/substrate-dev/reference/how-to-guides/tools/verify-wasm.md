# 验证 Wasm 二进制文件
subwasm工具提供了一种为任何 Substrate 链公开运行时功能的方法。本指南向您展示如何使用 Subwasm 执行以下操作：

获取运行时中模块的快照。
为前端应用程序公开链运行时的可验证历史信息。
使用差异检查两个运行时之间的更改。
在你开始之前
确保您具备以下条件：

安装了最新版本的 Subwasm。
一个在本地运行的链，它正在执行您希望检查的运行时。
JQ安装在您的环境中以启用过滤元数据。
获取有关本地链运行时的信息
从您的链目录中启动一个本地节点。此示例在开发模式下启动节点模板。

./target/release/node-template --tmp --dev

在新终端中，创建一个名为TempWasms.

mkdir TempWasms

在新文件夹中，使用 Subwasm 获取并保存本地链的 Wasm。

cd TempWasms
subwasm get --chain local -o mychain.wasm

该-o标志会将 Wasm 文件保存到mychain.wasm.

运行以下命令将链的元数据保存到 JSON 文件：

subwasm --json meta mychain.wasm | jq 'del( .. | .documentation? )' > mychain-metadata.json

您可以使用 JQ 从subwasm --json meta mychain.wasm命令中过滤元数据详细信息。此示例忽略所有文档元数据。有关 Substrate 链公开的元数据字段的信息，请参阅有关Substrate 元数据的文档。

将元数据保存在 JSON 文件中后，您可以在前端应用程序中使用它。
比较实时链的两个不同运行时
您还可以使用 Subwasm 比较链在不同区块的运行时间。Subwasm 使用一个函数来获取常见 Substrate 链（如 Polkadot、Kusama 和 Westend）的 URL，并返回节点的端点。以下示例将 500,000 块的 Polkadot 运行时与其最新运行时进行比较。

转到块资源管理器并检索要比较的运行时的块哈希。

例如，转到PolkadotJS 应用程序并检索块 500000处的块哈希。

0xfdeab19649426aadbdab046c9b5dac0a5b0c850b8fd0927e3b4e6c896d5fb0b7

保存先前状态的 Wasm blob。

BLOCK_HASH=0xfdeab19649426aadbdab046c9b5dac0a5b0c850b8fd0927e3b4e6c896d5fb0b7
subwasm get --chain polkadot --block $BLOCK_HASH -o polkadot-500000.wasm

获取运行时的最新 Wasm blob。

subwasm get --chain polkadot -o polkadot-latest.wasm

比较两个运行时并将输出放入 JSON 文件。

subwasm diff polkadot-latest.wasm polkadot-500000.wasm --json > polkadot-wasm-diff.json

例子
subwasm如上所述，有许多用例。一些额外的例子包括：

压缩和解压缩 Wasm 文件。
检查 Wasm 文件以验证治理中的运行时升级。
