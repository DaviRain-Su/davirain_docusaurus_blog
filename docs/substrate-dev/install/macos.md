# macOS 开发环境

您可以在配备 Intel 或 Apple M1 处理器的 Apple macOS 计算机上安装 Rust 并设置 Substrate 开发环境。

在你开始之前
在 macOS 上安装 Rust 并设置开发环境之前，请确认您的计算机满足以下基本要求：

操作系统版本为 10.7 Lion 或更高版本。
处理器速度至少为 2Ghz，建议使用 3Ghz。
内存至少 8 GB RAM，建议使用 16 GB。
存储空间为 10 GB 可用空间。
宽带互联网连接。
支持苹果 M1
如果您的 macOS 计算机具有片上 Apple M1 ARM 系统，则必须安装 Apple Rosetta 才能protoc在 Rust 构建过程中运行该工具。Rust 工具链和目标二进制文件仍然是原生的。您可以通过打开终端应用程序并运行以下命令在 macOS 计算机上安装 Rosetta：

softwareupdate --install-rosetta

安装自制软件
在大多数情况下，您应该使用 Homebrew 在 macOS 计算机上安装和管理软件包。如果您尚未在本地计算机上安装 Homebrew，则应在继续之前下载并安装它。

要安装 Homebrew：

打开终端应用程序。
通过运行以下命令下载并安装 Homebrew：

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

通过运行以下命令验证 Homebrew 是否已成功安装：

brew --version

该命令显示类似于以下内容的输出：

Homebrew 3.3.1
Homebrew/homebrew-core (git revision c6c488fbc0f; last commit 2021-10-30)
Homebrew/homebrew-cask (git revision 66bab33b26; last commit 2021-10-30)

安装所需的包和 Rust
因为区块链需要标准密码学来支持公钥/私钥对的生成和交易签名的验证，所以你还必须有一个提供密码学的包，例如openssl.

openssl在 macOS 上安装Rust 工具链：

打开终端应用程序。
通过运行以下命令确保您拥有更新版本的 Homebrew：

brew update

openssl通过运行以下命令安装包：

brew install openssl

下载rustup安装程序并通过运行以下命令使用它来安装 Rust：

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

按照显示的提示继续进行默认安装。
通过运行以下命令更新您当前的 shell 以包含 Cargo：

source ~/.cargo/env

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

stable-x86_64-apple-darwin (default)
rustc 1.61.0 (fe5b13d68 2022-05-18)

# rustup +nightly show

active toolchain
----------------

nightly-x86_64-apple-darwin (overridden by +toolchain on the command line)
rustc 1.63.0-nightly (e71440575 2022-06-02)

cmake使用以下命令安装：
brew install cmake
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

这里有一些额外的建议，您可以在哪里了解更多信息。

告诉我
建筑学
网络和区块链
构建过程
引导我
构建本地区块链
模拟网络
添加可信节点