# 工具

Substrate 运行时工具箱 ( srtool) 的核心组件是一个 Docker 容器，它使您能够以确定的方式构建 Substrate WebAssembly 运行时。通过使用此工具，您可以确保相同的源代码始终如一地重现相同的 WebAssembly blob。您还可以使用该工具检查和审核任何基于 Substrate 的链的运行时，并将构建 WebAssembly 运行时集成到您的 CI/CD 管道中。

使用 Docker 容器
因为srtool是 Docker 容器，所以您必须在构建环境中提供 Docker 才能使用它。但是，您不需要了解有关使用 Docker 构建链的任何知识，srtool因为您可以使用srtool-cli命令行界面来处理 Docker 映像。

该srtiool-cli包是一个用 Rust 编写的命令行实用程序，它安装一个srtool在您的计算机上调用的可执行程序。该程序简化了您与srtoolDocker 容器的交互。随着时间的推移，围绕srtoolDocker 镜像的工具已经扩展到包括以下工具和帮助程序：

srtool-cli提供了一个命令行界面来拉取srtoolDocker 映像，获取有关映像和用于与之交互的工具的信息，并使用srtoolDocker 容器构建运行时。
subwasm提供了用于处理元数据和使用srtool. 该subwasm程序还用于在内部执行srtool映像中的任务。
srtool-actions提供 Github 操作以将使用映像生成的构建srtool与您的 GitHub CI/CD 管道集成。
srtool-appsrtool提供了一个简单的图形用户界面，用于使用Docker 映像构建运行时。
安装 srtool-cli
命令行界面使您能够使用Docker 映像srtool构建 WebAssembly 运行时。srtool您可以通过运行以下命令来安装srtool命令行界面：
```
cargo install --git https://github.com/chevdor/srtool-cli
```
基本命令用法
运行srtool命令的基本语法是：

`srtool [options] [subcommand]`
选项
您可以在命令中使用以下命令行选项srtool。

选项	描述
-h, --help	显示使用信息。
`i, --image <image>`	指定替代图像。请务必指定与默认图像兼容的paritytech/srtool图像。您应该注意，指定不同的图像可能不会产生与图像产生的相同的确定性结果paritytech/srtool。
-n, --no-cache 	指定您不想使用本地缓存中的标记值。
-V, --version 	显示版本信息。
子命令
您可以将以下子命令与该srtool命令一起使用。

命令	描述
build	启动一个新srtool容器来构建您的运行时。
help	显示srtool指定子命令的使用信息。
info	显示有关srtool容器和存储库的信息。
pull	拉取srtool图像而不运行其他任何东西。
version	显示srtool容器的版本信息。如果您想要可执行文件--version的版本信息，请使用。srtool-cli
例子
要获取srtoolDocker 映像的版本信息，请运行以下命令：
```
srtool version
```
该命令显示类似于以下内容的输出：
```
{
  "name": "srtool",
  "version": "0.9.21",
  "rustc": "1.62.0",
  "subwasm": "0.18.0",
  "tera": "0.2.1",
  "toml": "0.2.1"
}
```
要获取srtool-cli可执行文件的版本信息，请运行以下命令：
```
srtool --version
```
该命令显示类似于以下内容的输出：

srtool-cli 0.8.0
srtool 构建
使用该srtool build命令启动一个新srtool容器，为您指定的包构建运行时。默认情况下，该srtool build命令假定Cargo.toml运行时文件位于runtime具有链名称的子目录中。例如，该srtool build命令默认使用以下位置：

运行时/kusama
运行时/polkadot
运行时/洛可可
运行时/西端
如果Cargo.toml您的运行时文件位于不同的位置，您可以将路径指定为命令行选项。

基本用法
运行srtool build命令的基本语法是：

`srtool build [options] --package <package> [--runtime-dir <path>] [project-path]`
论据
默认情况下，srtool build在当前工作目录中运行。如果您的项目不在当前工作目录中，您可以指定项目位置的路径。

争论	描述
project-path	指定要为其构建运行时的区块链项目的路径。
选项
您可以在命令中使用以下命令行选项srtool build。

选项	描述
-a, --app	在构建期间启用标准输出和 JSON 输出的混合。建议将此选项用于 CI。JSON 输出在构建结束时以单行形式提供。
`--build-opts <BUILD_OPTS>`	使您能够将自定义选项直接传递给cargo构建过程。如果您指定此命令行选项，请注意构建 Kusama 或 Polkadot 的任何自动选项都不会传递到构建过程。--build-opts使用命令行选项时，必须显式设置所需的构建选项。通常，很少需要此选项。该选项相当于设置BUILD_OPTS环境变量。
`--default-features <default-features>`	使您能够更改运行时的默认功能列表，而无需禁用自动功能检测。该选项相当于设置DEFAULT_FEATURES环境变量。如果您设置BUILD_OPTS.
-h, --help	显示使用信息。
`i, --image <image>`	指定替代图像。请务必指定与默认图像兼容的paritytech/srtool图像。您应该注意，指定不同的图像可能不会产生与图像产生的相同的确定性结果paritytech/srtool。
-j, --json	启用 JSON 输出。
--no-cache	禁用所有缓存。如果您指定此选项，srtool图像将不会访问 Cargo 主缓存以获取构建依赖项。通常，很少使用此选项，因为使用缓存没有已知问题。
`-p, --package <package>`	指定要构建的运行时包的名称。您指定的名称应与Cargo.toml文件中为运行时定义的名称相同，例如 kusama-runtime、polkadot-runtime 等。该选项相当于设置PACKAGE环境变量。
`--profile <profile>`	指定用于构建运行时的配置文件，构建运行时的默认配置文件始终为release。您可以使用此命令行选项覆盖默认值。该选项相当于设置PROFILE环境变量。
`-r, --runtime-dir <runtime>`	指定Cargo.toml运行时文件的位置。如果您的运行时不在标准位置，您可以使用此命令行选项来指定正确的位置。该选项相当于设置RUNTIME_DIR环境变量。
-V, --version	显示版本信息。
例子
要从运行时cumulus路径Cargo.toml为parachains/runtimes/assets/westmint的存储库构建 Westmint 运行时，您将运行以下命令：
```
srtool build --app --package westmint-runtime --runtime-dir parachains/runtimes/assets/westmint
```
第一次运行srtool build命令需要一些时间才能完成。在运行时编译时，有关其进度的消息将显示为标准输出。由于此示例使用--app命令行选项，因此 JSON 输出在构建结束时以单行显示，类似于以下截断输出：
```
...
   Compiling cumulus-primitives-parachain-inherent v0.1.0 (/build/primitives/parachain-inherent)
   Compiling cumulus-pallet-parachain-system v0.1.0 (/build/pallets/parachain-system)
    Finished release [optimized] target(s) in 112m 11s
✨ Your Substrate WASM Runtime is ready! ✨
{"gen":"srtool v0.9.21","src":"git","version":"1.0.0","commit":"bd41e3f11887ea2f55fc37be71ff652923388e03","tag":"v0.9.220-rc2","branch":"master","rustc":"rustc 1.62.0 (a8314ef7d 2022-06-27)","pkg":"westmint-runtime","tmsp":"2022-08-22T21:12:18Z","size":"707937","prop":"0x6b8e93443b6660a16f67a6cd34d415af463e2285eda3fd02b9fe052c1ad2ceb9"
... }}}}
```
工具帮助
使用该srtool help命令显示srtool指定子命令的使用消息。

基本用法
`srtool help [subcommand]`
例子
要显示 build 子命令的使用信息，请运行以下命令：
```
subkey help build
```
工具信息
使用该srtool info命令显示有关srtool容器和存储库的信息。默认情况下，该srtool info命令假定Cargo.toml运行时文件位于runtime具有链名称的子目录中。例如，该srtool info命令默认使用以下位置：

运行时/kusama
运行时/polkadot
运行时/洛可可
运行时/西端
如果Cargo.toml您的运行时文件位于不同的位置，您可以将路径指定为命令行选项。

基本用法
运行srtool info命令的基本语法是：

`srtool info [options] --package <package> [--runtime-dir <path>] [project-path]`
论据
默认情况下，srtool info在当前工作目录中运行。如果您的项目不在当前工作目录中，您可以指定项目位置的路径。

争论	描述
project-path	如果项目不在当前工作目录中，则指定区块链项目的路径。
选项
您可以在命令中使用以下命令行选项srtool info。

选项	描述
-h, --help	显示使用信息。
`i, --image <image>`	指定替代图像。请务必指定与默认图像兼容的paritytech/srtool图像。您应该注意，指定不同的图像可能不会产生与图像产生的相同的确定性结果paritytech/srtool。
`-p, --package <package>`	指定要构建的运行时包的名称。您指定的名称应与Cargo.toml文件中为运行时定义的名称相同，例如 kusama-runtime、polkadot-runtime 等。该选项相当于设置PACKAGE环境变量。
`-r, --runtime-dir <runtime>`	指定Cargo.toml运行时文件的位置。如果您的运行时不在标准位置，您可以使用此命令行选项来指定正确的位置。该选项相当于设置RUNTIME_DIR环境变量。
-V, --version	显示版本信息。
例子
要显示有关srtool容器和本地节点模板存储库的信息，您可以运行类似于以下内容的命令：
```
srtool info --package node-template-runtime --runtime-dir runtime
```
此命令显示类似于以下内容的输出：
```
{
  "generator": {
    "name": "srtool",
    "version": "0.9.21"
  },
  "src": "git",
  "version": "4.0.0-dev",
  "git": {
    "commit": "6a8b2b12371395979099d2c79ccc1860531b0449",
    "tag": "",
    "branch": "my-branch-v0.9.28"
  },
  "rustc": "rustc 1.62.0 (a8314ef7d 2022-06-27)",
  "pkg": "polkadot-runtime",
  "profile": "release"
}
```
工具拉
使用该srtool pull命令检查并下载最新版本的srtoolDocker 映像。

基本用法
运行srtool pull命令的基本语法是：

`srtool pull [options]`
选项
您可以在命令中使用以下命令行选项srtool pull。

选项	描述
-h, --help	显示使用信息。
`i, --image <image>`	指定替代图像。请务必指定与默认图像兼容的paritytech/srtool图像。您应该注意，指定不同的图像可能不会产生与图像产生的相同的确定性结果paritytech/srtool。
-V, --version	显示版本信息。
例子
要检查srtool容器和 Docker 映像的新版本，您可以运行类似于以下的命令：
```
srtool pull
```
此命令检查 Docker Hub 以获取最新版本的paritytech/srtool映像并开始下载和解压缩软件。例如
```
Found 1.62.0, we will be using paritytech/srtool:1.62.0 for the build
1.62.0: Pulling from paritytech/srtool
405f018f9d1d: Pull complete 
c49473e7f7b3: Pull complete 
7edf98d07029: Pull complete 
85a50724a6fa: Pull complete 
87fb1e3dee5b: Downloading   19.4MB/170.4MB
469075c5d317: Download complete 
533bfa44b64a: Download complete 
...
```
完成所有任务后，该命令会显示类似于以下内容的输出：
```
Digest: sha256:d5353a63d8fccbef5666e28a8fa0b302d71d4f53cabeb760fe213f3d7df4b8b6
Status: Downloaded newer image for paritytech/srtool:1.62.0
docker.io/paritytech/srtool:1.62.0
```
如果您已经在本地安装了最新版本，该命令会显示类似于以下内容的输出：
```
Found 1.62.0, we will be using paritytech/srtool:1.62.0 for the build
1.62.0: Pulling from paritytech/srtool
Digest: sha256:d5353a63d8fccbef5666e28a8fa0b302d71d4f53cabeb760fe213f3d7df4b8b6
Status: Image is up to date for paritytech/srtool:1.62.0
docker.io/paritytech/srtool:1.62.0
```
srtool 版本
使用srtool version命令显示srtool容器的版本信息。--version如果您想要srtool-cli可执行文件的版本，请使用。

基本用法
运行srtool version命令的基本语法是：

`srtool version [options]`
选项
您可以在命令中使用以下命令行选项srtool version。

选项	描述
-h, --help	显示使用信息。
`i, --image <image>`	指定替代图像。请务必指定与默认图像兼容的paritytech/srtool图像。您应该注意，指定不同的图像可能不会产生与图像产生的相同的确定性结果paritytech/srtool。
-V, --version	显示版本信息。
例子
要显示有关 srtool 容器的信息，请运行以下命令：
```
srtool version       
```
该命令显示类似于以下内容的输出：
```
{
  "name": "srtool",
  "version": "0.9.21",
  "rustc": "1.62.0",
  "subwasm": "0.18.0",
  "tera": "0.2.1",
  "toml": "0.2.1"
}
```
要显示srtool命令行界面而不是容器的版本信息，可以运行以下命令：
```
srtool version --version
```
该命令显示类似于以下内容的输出：
```
srtool-version 0.8.0
```