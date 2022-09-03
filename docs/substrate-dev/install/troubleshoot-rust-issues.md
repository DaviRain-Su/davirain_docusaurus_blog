# 解决 Rust 问题

如果编译Substrate 节点模板失败，则问题很可能是由您的开发环境中的 Rust 配置方式引起的。本节建议您如何诊断和修复配置问题。

检查您当前的配置
要查看有关您当前使用的 Rust 工具链的信息，请运行以下命令：
```
rustup show
```
此命令显示类似于以下 Ubuntu 示例的输出：
```
Default host: x86_64-unknown-linux-gnu
rustup home:  /home/user/.rustup

installed toolchains
--------------------

stable-x86_64-unknown-linux-gnu (default)
nightly-2020-10-06-x86_64-unknown-linux-gnu
nightly-x86_64-unknown-linux-gnu

installed targets for active toolchain
--------------------------------------

wasm32-unknown-unknown
x86_64-unknown-linux-gnu

active toolchain
----------------

stable-x86_64-unknown-linux-gnu (default)
rustc 1.50.0 (cb75ad5db 2021-02-10)
```
在此示例中，默认工具链来自在 x86 64 架构stable上运行的 Linux 的发布通道。示例输出还表明安装了“nightly-x86 64-unknown-linux-gnu”工具链，并且安装了两个目标：

x86_64-unknown-linux-gnuLinux 的原生 Rust 目标。
wasm32-unknown-unknownWebAssembly 目标。
此环境还nightly-2020-10-06-x86_64-unknown-linux-gnu安装了工具链，但仅当明确指定为命令行选项时才使用此工具链。有关将特定工具链指定为命令行选项的示例，请参阅指定夜间版本。

使用 WebAssembly 的每晚发布频道
Substrate 使用WebAssembly (Wasm) 来生成可移植的区块链运行时。您必须将 Rust 编译器配置为使用nightly构建，以允许您将 Substrate 运行时代码编译到 Wasm 目标。

更新工具链
通常，您应该始终使用最新版本的 Ruststable和nightly构建，因为 Substrate 中的更改通常取决于 Rustnightly编译器构建中的上游更改。为确保您的 Rust 编译器始终是最新的，您应该运行以下命令：
```
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
```
运行rustup update更新nightly和stable工具链以使用最新版本。如果您在更新工具链后无法编译 WebAssembly 目标nightly，您可以回滚到工具链的早期版本并将该版本指定为命令行选项。有关获取较早版本的nightly工具链和指定要用作命令行选项的版本的更多信息，请降级工具链。

使用特定的夜间工具链
如果你想保证你的构建在你更新 Rust 和其他依赖项时可以在你的计算机上运行，​​你应该使用一个nightly你知道与你正在使用的 Substrate 版本兼容的特定 Rust 工具链。您如何识别和传达nightly用于项目的特定工具链版本可能会有所不同。例如，Polkadot 在其发行说明中发布了此信息。

在您确定要使用的特定nightly工具链版本后，您可以通过运行类似于以下的命令将其安装到您的开发环境中：
```
rustup install nightly-<yyyy-MM-dd>
```
例如：
```
rustup install nightly-2022-02-16
```
安装特定版本的 nightly 工具链后，通过运行类似于以下的命令配置 WebAssembly 目标以使用它：
```
rustup target add wasm32-unknown-unknown --toolchain nightly-<yyyy-MM-dd>
```
例如：
```
rustup target add wasm32-unknown-unknown --toolchain nightly-2022-02-16
```
在环境变量中指定工具链
您可以设置WASM_BUILD_TOOLCHAIN环境变量来指定nightly用于编译 WebAssembly 的工具链的版本。例如：
```
WASM_BUILD_TOOLCHAIN=nightly-<yyyy-MM-dd> cargo build --release
```
此命令使用指定的夜间工具链构建运行时。项目的其余部分使用默认stable工具链编译，即您已安装的最新版本的工具链。

降级夜间工具链
如果您的计算机配置为使用最新的 Rustnightly工具链并且您想降级到特定的夜间版本，您必须首先卸载最新的nightly工具链。例如，您可以删除最新的工具链，然后通过运行类似于以下的命令来nightly使用特定版本的工具链：nightly
```
rustup uninstall nightly
rustup install nightly-<yyyy-MM-dd>
rustup target add wasm32-unknown-unknown --toolchain nightly-<yyyy-MM-dd>
```
确保 PATH 设置正确
如果安装 Rust 后命令似乎不起作用，显示诸如 之类的错误command not found: rustup，请确保您的 PATH 配置正确。

目前，rustup安装程序默认安装到 bash 配置文件（在 mac 上）。如果您使用的是另一个 shell，请确保将此行添加到您的配置文件中（例如.zshrc）：
```
source "$HOME/.cargo/env"
```
为 M1 macOS 用户安装 cmake 或 protobuf
目前，在使用预装在具有 M1 芯片的 macOS 计算机上的软件包时，编译 Substrate 节点存在问题。
```
error: failed to run custom build command for prost-build v0.10.4
```
如果您看到此错误，有两种解决方案。

cmake通过运行以下命令进行安装：
```
brew install cmake
```
protoc通过运行以下命令集安装正确的预编译：
```
git clone https://github.com/protocolbuffers/protobuf.git
cd protobuf

brew install autoconf
brew install automake
brew install Libtool

autoreconf -i
./autogen.sh
./configure
make
make check
sudo make install

export PATH=/opt/usr/local/bin:$PATH
```