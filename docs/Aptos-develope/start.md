# Aptos Network 的Move开发指南

## Aptos Network 网络环境设置

### aptpos-cli的源码安装

#### 克隆aptos-core

从github上clone aptos-core存储库。

1. 克隆 aptos core

```bash
git clone https://github.com/aptos-labs/aptos-core.git
```

2. 进入aptos-core目录

```bash
cd aptos-core
```

3. 运行`scripts/dev-setup.sh` bash脚本，如下所示。这将通过安装构建、测试和检查Aptos Core所需的大部分依赖项来准备你的开发人员环境。需要注意的是系统可能会提示你输入密码:

```bash
./scripts/dev_setup.sh
```

> 可以通过运行查看脚本的可用选项`./scripts/dev_setup.sh --help`

4. 更新当前的shell环境以运行`cargo build`和其他与aptos相关的命令

```bash
source ~/.cargo/evn
```

5. 可选 签出发布分支以安装aptos节点

```bash 
git checkout --track origin/devnet # devnet

git checkout --track origin/testnet-stable # testnet
```

6. 执行`cargo build` 编译出aptos-cli等执行文件

```bash
cargo build -release
```

### 直接安装aptos-cli

该aptos工具是一个命令行界面 (CLI)，用于在 Aptos 区块链上进行开发、调试 Move 合约和执行节点操作。本文档介绍如何安装aptosCLI 工具。

通过为您的平台下载预编译的二进制文件来安装 CLI，如下所述。

#### 下载预编译的二进制文件

##### 对于mac系统

1. 转至[Aptos CLI](https://github.com/aptos-labs/aptos-core/releases?q=cli&expanded=true)版本列表。

2. 单击最新版本的资产可扩展菜单。

3. 您将看到带有以下格式文件名的 zip 文件：`aptos-cli-<version>-<platform>`. 这些是 CLI 的特定于平台的预编译二进制文件。下载适用于您平台的 zip 文件，忽略任何警告。

4. 解压下载的文件。这会将aptosCLI 二进制文件解压缩到您的默认下载文件夹中。例如，在 macOS 上，它是`~/Downloads`文件夹。

5. 将此提取aptos的二进制文件移动到您首选的本地文件夹中。例如，将它`~/bin/aptos`放在 macOS 上的文件夹中，以便从命令行访问它。

>升级？请记住查看默认下载文件夹 当您使用最新版本更新 CLI 二进制文件时，请注意，较新版本的二进制文件将下载到您的默认下载文件夹。请记住将此较新版本的二进制文件从“下载”文件夹移动到该`~/bin/aptos`文件夹​​以更新和覆盖旧版本。

6. `~/bin/aptos`通过运行以下命令使其成为可执行文件：

- `chmod +x ~/bin/aptos`.

- 在 macOS 上，当您第一次尝试运行该aptos工具时，您将被 macOS 阻止，认为此应用来自“未知开发者”。这个是正常的。按照 Apple 支持在从身份不明的开发者处打开 Mac 应用程序中推荐的简单步骤来删除此阻止程序。

7. 键入`~/bin/aptos help`以阅读帮助说明。

8. 添加~/bin到您的.bashrc或.zshrc文件中的路径以供将来使用。

##### 对于linux系统

1. 转至[Aptos CLI](https://github.com/aptos-labs/aptos-core/releases?q=cli&expanded=true)发布页面。

2. 单击最新版本的资产可扩展菜单。

3. 您将看到带有以下格式文件名的 zip 文件：`aptos-cli-<version>-<platform>`. 这些是 CLI 的特定于平台的预编译二进制文件。下载适用于您平台的 zip 文件，忽略任何警告。

4. 解压下载的文件。这会将aptosCLI 二进制文件解压缩到您的默认下载文件夹中。

5. 将此提取aptos的二进制文件移动到您首选的本地文件夹中。

> 升级？记得查看默认下载文件夹
>当您使用最新版本更新 CLI 二进制文件时，请注意，较新版本的二进制文件将下载到您的默认下载文件夹。请记住将此较新版本的二进制文件从下载文件夹移动到`~/bin/aptos`文件夹（覆盖旧版本）。

6. `~/bin/aptos`通过运行以下命令使其成为可执行文件：

- `chmod +x ~/bin/aptos`.

7. 键入`~/bin/aptos help`以阅读帮助说明。

8. 添加`~/bin`到您的`.bashrc`或`.zshrc`文件中的路径以供将来使用。

##### 对于windows系统

1. 转至[Aptos CLI](https://github.com/aptos-labs/aptos-core/releases?q=cli&expanded=true) 发布页面。

2. 单击最新版本的资产可扩展菜单。

3. 您将看到带有以下格式文件名的 zip 文件：`aptos-cli-<version>-<platform>`. 这些是 CLI 的特定于平台的预编译二进制文件。下载适用于您平台的 zip 文件，忽略任何警告。

4. 解压下载的文件。这会将aptosCLI 二进制文件解压缩到您的默认下载文件夹中。例如，在 Windows 上，它是`\Users\user\Downloads`文件夹。

5. 将此提取aptos的二进制文件移动到您首选的本地文件夹中。

>升级？记得查看默认下载文件夹
>当您使用最新版本更新 CLI 二进制文件时，请注意，较新版本的二进制文件将下载到您的默认下载文件夹。请记住将此较新版本的二进制文件从“下载”文件夹移动到您的首选位置。

6. 通过 windows 开始菜单打开一个 powershell 终端

7. 在 powershell 终端中，您可以通过运行带有帮助的命令来获取帮助说明。例如 `.\Downloads\aptos-cli-0.3.5-Windows-x86_64\aptos.exe help`阅读帮助说明。

#### 可选 安装move

如果要使用 Move Prover，请按照以下步骤安装依赖项：

> 目前，Move Prover 不支持 Windows。

##### Prover macOS安装

1. 确保您已brew安装 `https://brew.sh/`。

2. 确保您已git安装 `https://git-scm.com/book/en/v2/Getting-Started-Installing-Git`。

3. 克隆 Aptos 核心存储库： `git clone https://github.com/aptos-labs/aptos-core.git`.

4. 将目录更改为aptos-core：`cd aptos-core`

5. 运行开发设置脚本以准备您的环境：`./scripts/dev_setup.sh -yp`

6. 来源配置文件：`source ~/.profile`.

> 请注意，您必须在`~/.profileshell` 中包含环境变量定义。根据您的设置， `~/.profile`可能已经为每个登录 shell 自动加载，也可能不会。如果没有，您可能需要手动添加.` ~/.profile`到您的或其他 shell 配置。`~/.bash_profile`

7. 您现在可以运行 Move Prover 来证明一个示例：

```bash
aptos move prove --package-dir aptos-move/move-examples/hello_prover/
```

##### Prover linux安装

1. 确保您已git安装`https://git-scm.com/book/en/v2/Getting-Started-Installing-Git`。

2. 克隆 Aptos 核心存储库： `git clone https://github.com/aptos-labs/aptos-core.git`.

3. 将目录更改为aptos-core：`cd aptos-core`

4. 运行开发设置脚本以准备您的环境：`./scripts/dev_setup.sh -yp`

5. 来源配置文件：`source ~/.profile`.


> 请注意，您必须在`~/.profileshell` 中包含环境变量定义。根据您的设置， `~/.profile`可能已经为每个登录 shell 自动加载，也可能不会。如果没有，您可能需要手动添加. ~/.profile到您的或其他 shell 配置。`~/.bash_profile`

6. 您现在可以运行 Move Prover 来证明一个示例：

```bash
aptos move prove --package-dir aptos-move/move-examples/hello_prover/
```

### Mac工具安装

如果运行 macOS，请确保您的计算机上安装了以下工具。您将需要它们来按照指定的顺序运行开发者教程：

- brew ：https://brew.sh/

- Node.js：安装Node.js，它将通过在终端上执行以下命令来安装npm和npx：

```bash
brew install node
```

- Yarn ：通过在终端上执行以下命令来安装最新的Yarn ：

```bash
brew install yarn
```

- poetry：从 https://python-poetry.org/docs/#installation 安装诗歌。

现在您的基本 Aptos 开发环境已准备就绪。

### Aptos 开发者

本部分包含指向经常引用的 Aptos 开发人员资源的链接。

### Aptos 探索

- [Aptos Explorer](https://explorer.aptoslabs.com/)：使用右上角的下拉菜单选择网络。
- [Aptos 社区](https://aptoslabs.com/community)：论坛、Discord 和 AIT 的链接。
  

#### Aptos 测试网

- REST API 开放 API 规范：https://fullnode.testnet.aptoslabs.com/v1/spec#/
- REST 服务： https://fullnode.testnet.aptoslabs.com/v1
- 水龙头服务： https://faucet.testnet.aptoslabs.com
- 创世纪：https://testnet.aptoslabs.com/genesis.blob
- 创世纪和航点：https://github.com/aptos-labs/aptos-genesis-waypoint/tree/main/testnet
- ChainID：[单击此处在 Aptos Explorer 上查看](https://explorer.aptoslabs.com/?network=testnet)。

#### Aptos 开发网

- REST API 开放 API 规范：https://fullnode.devnet.aptoslabs.com/v1/spec#/
- REST 服务： https://fullnode.devnet.aptoslabs.com/v1
- 水龙头服务： https://faucet.devnet.aptoslabs.com
- 创世纪：https://devnet.aptoslabs.com/genesis.blob
- 航点：https://devnet.aptoslabs.com/waypoint.txt
- ChainID：[单击此处在 Aptos Explorer 上查看](https://explorer.aptoslabs.com/?network=devnet)。
  
#### Aptos Cli

- [Aptos CLI release](https://github.com/aptos-labs/aptos-core/releases?q=cli&expanded=true)
- [Aptos CLI 文档](https://aptos.dev/cli-tools/aptos-cli-tool/aptos-cli-index/)

#### Aptos SDK

- [Typescript SDK](https://www.npmjs.com/package/aptos)
- [Python SDK](https://pypi.org/project/aptos-sdk/)
- [RUST SDK](https://aptos.dev/sdks/rust-sdk/)

#### Move

- [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=damirka.move-syntax)的语法高亮显示: 
- [Visual Studio Code 的移动分析器](https://marketplace.visualstudio.com/items?itemName=move.move-analyzer)：支持高级代码导航和语法突出显示。
- [Jetbrains IDE 的移动语言插件](https://plugins.jetbrains.com/plugin/14721-move-language)：支持语法高亮、代码导航、重命名、格式化、类型检查和代码生成。