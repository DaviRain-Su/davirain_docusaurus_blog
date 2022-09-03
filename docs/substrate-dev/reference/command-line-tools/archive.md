# 档案
该archive程序用于为基于 Substrate 的链索引所有块、状态和交易数据，并将索引数据存储在关系 SQL 数据库中。该archive程序创建的数据库反映了来自正在运行的 Substrate 区块链的所有数据。归档数据后，您可以使用数据库查询和检索有关区块链状态的信息。有关您可能希望针对 Substrate 存档数据库运行的查询示例，请参阅有用的查询。

在你开始之前
在使用archive为基于 Substrate 的链创建数据库之前，您需要使用所需文件准备环境：

您必须在运行 Substrate 节点的计算机上安装 PostgreSQL。

您可以从 PostgreSQL下载页面下载不同平台的 PostgreSQL 包。

根据您的平台，您可能能够使用本地包管理器安装 PostgreSQL。例如，您可以通过brew install postgresql在终端中运行来在 macOS 计算机上安装 PostgreSQL 包。

您必须在安装了 PostgreSQL 的计算机上安装 RabbitMQ 或 Docker Compose。

根据您的平台，安装 RabbitMQ 或 Docker 的说明和系统要求可能会有所不同。有关使用RabbitMQ或 `[Docker(docker.com)]` 的信息，请参阅设置 substrate-archivewiki 页面。

您的 Substrate 链必须使用 RocksDB 作为其后端数据库。
安装和配置
要安装substrate-archive-cli程序：

在您的计算机上打开终端外壳。
substrate-archive通过运行以下命令克隆存储库：
```
git clone https://github.com/paritytech/substrate-archive.git
```
substrate-archive通过运行以下命令切换到存储库的根目录：
```
cd substrate-archive
```
在 Substrate 节点上启动 PostgreSQL 数据库 ( postgres) 和 Postgre 管理进程 ( )。pgadmin

如果你有 Docker Compose，你可以通过运行docker-compose up -d命令自动启动服务。

启动您的 Substrate 节点，并将其pruning设置为存档。例如： `./target/release/node-template --pruning=archive`
查看当前的数据库： `psql -U postgres -hlocalhost -p6432`
运行`DATABASE_URL=postgres://postgres:123@localhost:6432/local_chain_db sqlxdatabase create insubstrate-archive/src`创建数据库。
设置`CHAIN_DATA_DB="<your_path>"`。
设置你的archive.conf文件：

确保将基础浴设置为主数据库
告诉它rocksdb在哪里。使用 CHAIN DATA DB的状态
辅助数据库是一种优化
postgres url（如果在 prod 中设置为 var）
（可选）设置日志记录和调试。
运行节点模板。确保你运行它`--release --dev base-path=/tmp/dir --pruning=archive`
使用您的节点模板进行交易。
启动substrate-archive目标链的节点： `cargo run --release -- -c archive-conf.toml --chain=polkadot`
转到 PGAdmin URL localhost:15643/browser/#：。
查看参考以开始进行查询。
{附加上下文 - 可选}
基本命令用法
运行 { tool} 命令的基本语法是：

`tool [subcommand] [flag]`

标志
tool您可以在 { } 命令中使用以下可选标志。

子命令
您可以将以下子命令与 { tool} 命令一起使用。有关说明使用 { tool} 子命令的参考信息和示例，请选择适当的命令。

命令	描述
输出
根据您指定的子命令，{ tool} 程序的输出会显示以下部分或全部信息：

这个领域	包含
例子
要{描述一些用途}，请运行以下命令：

`tool --use`

{特定子命令}
使用 {tool subcommand} 命令来{子命令的作用}。

基本用法
`tool subcommand [FLAGS] [OPTIONS] <signature> <uri>`

标志
tool subcommand您可以在 { } 命令中使用以下可选标志。

旗帜	描述
选项
您可以将以下命令行选项与 { tool subcommand} 命令一起使用。

选项	描述
例子
{2-3 个关于如何使用此子命令的示例以及任何相关说明}