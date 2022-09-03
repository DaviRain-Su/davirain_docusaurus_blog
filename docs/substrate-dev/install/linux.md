# Linux开发环境

Rust 支持大多数 Linux 发行版。根据您使用的操作系统的特定发行版和版本，您可能需要将一些软件依赖项添加到您的环境中。一般来说，您的开发环境应该包括一个链接器或 C 兼容编译器，例如clang一个适当的集成开发环境 (IDE)。

在你开始之前
检查您的操作系统的文档以获取有关已安装软件包以及如何下载和安装您可能需要的任何其他软件包的信息。例如，如果您使用 Ubuntu，则可以使用 Ubuntu Advanced Packaging Tool ( apt) 来安装build-essential软件包：

sudo apt install build-essential

在安装 Rust 之前，您至少需要以下软件包：

clang curl git make
因为区块链需要标准密码学来支持公钥/私钥对的生成和交易签名的验证，所以你还必须有一个提供密码学的包，例如libssl-dev或openssl-devel。

安装所需的包和 Rust
在 Linux 上安装 Rust 工具链：

登录到您的计算机并打开终端外壳。
通过为您的 Linux 发行版运行适当的包管理命令来检查您已在本地计算机上安装的包。
通过为您的 Linux 发行版运行适当的包管理命令，将您缺少的任何包依赖项添加到本地开发环境中。

例如，在 Ubuntu Desktop 或 Ubuntu Server 上，您可能会运行类似于以下内容的命令：

sudo apt install --assume-yes git clang curl libssl-dev

单击选项卡标题以查看其他 Linux 操作系统的示例：


Debian

拱

软呢帽

开放使用
sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler

请记住，不同的发行版可能会以不同的方式使用不同的包管理器和捆绑包。例如，根据您的安装选择，Ubuntu Desktop 和 Ubuntu Server 可能具有不同的软件包和不同的要求。但是，命令行示例中列出的软件包适用于许多常见的 Linux 发行版，包括 Debian、Linux Mint、MX Linux 和 Elementary OS。

下载rustup安装程序并通过运行以下命令使用它来安装 Rust：

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

按照显示的提示继续进行默认安装。
通过运行以下命令更新您当前的 shell 以包含 Cargo：

source $HOME/.cargo/env

通过运行以下命令来验证您的安装：

rustc --version

通过运行以下命令将 Rust 工具链配置为默认为最新的稳定版本：

rustup default stable
rustup update

通过运行以下命令将nightly发布和WebAssembly (wasm) 目标添加到您的开发环境中：nightly

rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly

通过运行以下命令来验证您的开发环境的配置：

rustup show
rustup +nightly show

该命令显示类似于以下内容的输出：

# rustup show

active toolchain
----------------

stable-x86_64-unknown-linux-gnu (default)
rustc 1.62.1 (e092d0b6b 2022-07-16)

# rustup +nightly show

active toolchain
----------------

nightly-x86_64-unknown-linux-gnu (overridden by +toolchain on the command line)
rustc 1.65.0-nightly (34a6cae28 2022-08-09)

编译一个 Substrate 节点
现在您已经安装了 Rust 并为 Substrate 开发配置了 Rust 工具链，您可以通过克隆 Substrate节点模板文件并编译 Substrate 节点来完成开发环境的设置。

节点模板提供了一个工作环境，其中包含构建区块链所需的所有最常见功能，而无需任何无关模块或工具。为确保节点模板为您提供相对稳定的工作环境供您试验，建议的最佳实践是从 Substrate Developer Hub 存储库中克隆 Substrate 节点模板，而不是从核心 Substrate 存储库中克隆。

编译 Substrate 节点模板：

通过运行以下命令克隆节点模板存储库：

git clone --branch polkadot-v0.9.25 https://github.com/substrate-developer-hub/substrate-node-template

通过运行以下命令切换到节点模板目录的根目录：

cd substrate-node-template

通过运行以下命令编译节点模板：

cargo build --release

由于需要的软件包数量，编译节点可能需要几分钟时间。

构建成功完成后，您的本地计算机已准备好进行 Substrate 开发活动。

下一步去哪里
Substrate 开发者中心充当访问社区可用资源的中央门户。根据您的兴趣和学习方式，您可能更喜欢一种途径而不是另一种途径。例如，如果您喜欢阅读源代码并熟悉 Rust，您可能希望从深入研究Rust API开始。

告诉我
建筑学
网络和区块链
构建过程
引导我
构建本地区块链
模拟网络
添加可信节点