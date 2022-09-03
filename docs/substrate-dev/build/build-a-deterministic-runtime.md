# 构建确定性运行时

默认情况下，Rust 编译器会生成优化的 WebAssembly 二进制文件。这些二进制文件非常适合在您进行本地开发的隔离环境中工作。但是，不能保证编译器默认构建的 WebAssembly 二进制文件具有确定性可重现性。每次编译器生成 WebAssembly 运行时，它可能会生成稍微不同的 WebAssembly 字节码。这在所有节点必须使用完全相同的原始链规范文件的区块链网络中是有问题的。

使用不能保证确定性可重现的构建也可能导致其他问题。例如，如果您想自动化区块链的构建过程，您需要确保相同的代码始终产生相同的结果。如果没有确定性构建，每次推送都编译 WebAssembly 运行时会产生不一致和不可预测的结果，从而难以与任何自动化集成，并可能不断破坏您的 CI/CD 管道。确定性构建（始终编译为完全相同的字节码的代码）还确保可以检查、审核和独立验证 WebAssembly 运行时。

WebAssembly 运行时工具
为了帮助您以确定的方式编译 WebAssembly 运行时，您可以使用为 Polkadot、Kusama 和其他基于 Substrate 的链生成运行时的相同工具。该工具（统称为 Substrate 运行时工具箱或srtool）确保相同的源代码始终编译为相同的 WebAssembly blob。

Substrate 运行时工具箱 ( srtool) 的核心组件是一个 Docker 容器。此容器作为 Docker 映像的一部分执行。Docker 镜像的名称srtool指定了用于编译镜像中包含的代码的 Rust 编译器的版本。例如，图像paritytech/srtool:1.62.0表明图像中的代码是使用编译器版本1.62.0编译的rustc。

使用 Docker 容器
因为srtool是 Docker 容器，所以您必须在构建环境中提供 Docker 才能使用它。但是，您无需了解有关使用 Docker 构建基于 Substrate 的链的任何知识，srtool因为您可以使用srtool-cli命令行界面来处理 Docker 映像。

该srtiool-cli包是一个用 Rust 编写的命令行实用程序，它安装一个srtool在您的计算机上调用的可执行程序。该程序简化了您与 srtool Docker 容器的交互。随着时间的推移，围绕srtoolDocker 镜像的工具已经扩展到包括以下工具和帮助程序：

srtool-cli提供了一个命令行界面来拉取 srtool Docker 镜像，获取有关镜像和与之交互的工具的信息，并使用srtoolDocker 容器构建运行时。
subwasm提供了使用 srtool 构建的元数据和 WebAssembly 运行时的命令行选项。该subwasm程序还用于在内部执行srtool映像中的任务。
srtool-actions提供 GitHub 操作，以将使用映像生成的构建srtool与您的 GitHub CI/CD 管道集成。
srtool-appsrtool提供了一个简单的图形用户界面，用于使用Docker 映像构建运行时。
准备环境
要使用在 Docker 容器中执行代码的 Docker 映像srtool，您必须拥有 Docker 帐户和可用的 Docker 命令行或桌面工具。您还应该使用您想要使用的特定命令行工具来准备您的开发环境。至少，您应该安装该srtool-cli程序以使您能够使用简单的命令行界面使用 Docker 魔术师。

准备环境：

在您的 Substrate 开发环境中打开一个终端 shell。
通过运行以下命令验证您是否已安装 Docker：

docker --version

如果安装了 Docker，该命令会显示版本信息。例如：

Docker version 20.10.17, build 100c701
通过运行以下命令安装srtool命令行界面：

cargo install --git https://github.com/chevdor/srtool-cli

通过运行以下命令查看srtool命令行界面的使用信息：

srtool help

srtool通过运行以下命令下载最新的Docker 映像：

srtool pull

开始确定性构建
准备好环境后，您可以开始使用srtoolDocker 映像编译 WebAssembly 运行时。

要构建运行时：

在您的 Substrate 开发环境中打开一个终端 shell。
通过运行类似于以下的命令来编译项目的运行时：

srtool build --app --package node-template-runtime --runtime-dir runtime

您为 指定的--package名称应该是在Cargo.toml文件中为运行时定义的名称。
您为 指定的--runtime-dir路径应该是 Cargo.toml运行时文件的路径。如果Cargo.toml运行时文件位于runtime子目录中——例如，runtime/kusama——你可以省略 --runtime-dir命令行选项。
添加工作流操作
如果您为基于 Substrate 的项目使用 GitHub 存储库，则可以设置 GitHub 工作流程以开始自动编译 WebAssembly 运行时。

添加用于构建运行时的工作流：

.github/workflows在您的 Substrate 存储库中创建一个目录。
在.github/workflows目录中，单击Add file，然后选择Create new file。
从srtools-actionsbasic.yml存储库中的示例中复制示例 GitHub 操作，并将其粘贴到您在上一步中创建的文件中。
修改示例操作中的设置以适合您的链。

例如，修改以下设置：

链的名称
运行时包的名称
运行时的位置
在您的 Substrate 存储库中键入操作文件的名称。
单击提交新文件。
从 Docker Hub 下载
您必须在构建环境中安装 Docker 帐户和 Docker 才能使用 Substrate 运行时工具箱。如果您登录到 Docker Hub，您可以搜索paritytech/srtool容器并找到带有标识 Rust 编译器版本和构建脚本版本的标签名称的相应镜像。

如果您不想使用srtool-cli或srtool-app使用 paritytech/srtool容器，可以paritytech/srtool直接从 Docker Hub 拉取容器映像。

从 Docker Hub 拉取镜像：

登录到 Docker 中心。
在搜索字段中输入paritytech/srtool，然后按 Enter。
点击paritytech/srtool，然后点击标签。
复制要拉取的图像的命令。
在本地计算机上打开终端 shell。
粘贴从 Docker Hub 复制的命令。

例如，您可能会运行类似于以下的命令：

docker pull paritytech/srtool:1.62.0

该命令下载并解压缩图像。

图像的命名约定
与许多 Docker 镜像不同，镜像没有latest标签srtool。如果您直接从 Docker Hub 下载镜像，则需要选择与您安装的 Rust 编译器版本兼容的镜像。Docker 镜像的命名约定paritytech/srtool指定了用于编译镜像中包含的代码的 Rust 编译器的版本。还有一些图像指定了编译器版本和使用的构建脚本的版本。例如，一个名为的图像paritytech/srtool:1.62.0-0.9.19是使用编译器版本编译1.62.0的，rustc但使用的0.9.19是构建脚本的版本。

仅指定编译器版本的图像始终包含最新版本的软件。在大多数情况下，这就是您应该使用的图像。

编译器版本
如果您直接从 Docker Hub 下载镜像，您应该首先检查您使用的 Rust 编译器的版本，以确保该镜像与您安装的 Rust 版本兼容。如果您不确定您使用的是哪个版本的 Rust 编译器，您可以运行以下命令来检查您安装的版本：

rustc --version