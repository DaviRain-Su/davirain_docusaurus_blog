# 视窗开发环境

通常，基于 UNIX 的操作系统（如 macOS 或 Linux）为构建基于 Substrate 的区块链提供了更好的开发环境。Substrate教程和操作指南中的所有代码示例和命令行说明都说明了如何在终端中使用与 UNIX 兼容的命令与 Substrate 交互。

但是，如果您的本地计算机使用 Microsoft Windows 而不是基于 UNIX 的操作系统，您可以使用其他软件对其进行配置，使其成为构建基于 Substrate 的区块链的合适开发环境。要在运行 Microsoft Windows 的计算机上准备开发环境，您可以使用 Windows Subsystem for Linux (WSL) 来模拟 UNIX 操作环境。

在你开始之前
在 Microsoft Windows 上安装之前，请验证以下基本要求：

您有一台运行受支持版本的 Microsoft Windows 操作系统的计算机。
您必须运行 Microsoft Windows 10 版本 2004 或更高版本，或者 Microsoft Windows 11，才能在具有 Windows 桌面操作系统的计算机上安装适用于 Linux 的 Windows 子系统。
您必须运行 Microsoft Windows Server 2019 或更高版本，才能在具有 Windows 服务器操作系统的计算机上安装适用于 Linux 的 Windows 子系统。
您有良好的互联网连接并可以访问本地计算机上的 shell 终端。
为 Linux 设置 Windows 子系统
适用于 Linux 的 Windows 子系统 (WSL) 使您能够在使用 Windows 操作系统的计算机上模拟 Linux 环境。这种方法用于 Substrate 开发的主要优点是您可以使用 Substrate 文档中描述的所有代码和命令行示例。例如，您可以运行常用命令，如ls和-ps未修改。通过使用适用于 Linux 的 Windows 子系统，您可以避免配置虚拟机映像或双启动操作系统。

要使用适用于 Linux 的 Windows 子系统准备开发环境：

检查您的 Windows 版本和内部版本号，以查看是否默认启用了适用于 Linux 的 Windows 子系统。

如果您拥有 Microsoft Windows 10 版本 2004（Build 19041 及更高版本）或 Microsoft Windows 11，则默认情况下可以使用适用于 Linux 的 Windows 子系统，您可以继续下一步。

如果您安装了旧版本的 Microsoft Windows，请参阅旧版本的WSL 手动安装步骤。如果您在较旧版本的 Microsoft Windows 上进行安装，并且您的计算机具有 Windows 10 版本 1903 或更高版本，则可以下载并安装 WLS 2。

从开始菜单中选择 Windows PowerShell 或命令提示符，右键单击，然后以管理员身份运行。
在 PowerShell 或命令提示符终端中，运行以下命令：

wsl --install

此命令启用作为 Windows 操作系统一部分的所需 WSL 2 组件，下载最新的 Linux 内核，并默认安装 Ubuntu Linux 发行版。

如果要查看其他可用的 Linux 发行版，请运行以下命令：

wsl --list --online

下载发行版后，关闭终端。
单击开始菜单，选择关机或注销，然后单击重新启动以重新启动计算机。

需要重新启动计算机才能开始安装 Linux 发行版。重新启动后，安装可能需要几分钟才能完成。

有关将 WSL 设置为开发环境的更多信息，请参阅设置 WSL 开发环境。

安装所需的包和 Rust
在 WSL 上安装 Rust 工具链：

单击开始菜单，然后选择Ubuntu。
键入 UNIX 用户名以创建用户帐户。
键入 UNIX 用户的密码，然后重新键入密码进行确认。
apt通过运行以下命令，使用 Ubuntu 高级打包工具 ( ) 下载 Ubuntu 发行版的最新更新：

sudo apt update

通过运行以下命令为 Ubuntu 发行版添加所需的软件包：

sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler

下载rustup安装程序并通过运行以下命令使用它为 Ubuntu 发行版安装 Rust：

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

按照显示的提示继续进行默认安装。
通过运行以下命令更新您当前的 shell 以包含 Cargo：

source ~/.cargo/env

通过运行以下命令来验证您的安装：

rustc --version

通过运行以下命令，将 Rust 工具链配置为使用最新的稳定版本作为默认工具链：

rustup default stable
rustup update

通过运行以下命令，将工具链的nightly版本和nightlyWebAssembly ( ) 目标添加到您的开发环境中：wasm

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
rustc 1.61.0 (fe5b13d68 2022-05-18)

# rustup +nightly show

active toolchain
----------------

nightly-x86_64-unknown-linux-gnu (overridden by +toolchain on the command line)
rustc 1.63.0-nightly (e71440575 2022-06-02)

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