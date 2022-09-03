# 监控节点指标
Substrate 公开有关网络运行的指标。例如，您可以收集有关您的节点连接到多少对等点以及您的节点正在使用多少内存的信息。要可视化这些指标，您可以使用Prometheus和Grafana等工具。本教程演示了如何使用 Grafana 和 Prometheus 来抓取和可视化这些类型的节点指标。

可能的架构可能如下所示：

+-----------+                     +-------------+                                                              +---------+
| Substrate |                     | Prometheus  |                                                              | Grafana |
+-----------+                     +-------------+                                                              +---------+
      |               -----------------\ |                                                                          |
      |               | Every 1 minute |-|                                                                          |
      |               |----------------| |                                                                          |
      |                                  |                                                                          |
      |        GET current metric values |                                                                          |
      |<---------------------------------|                                                                          |
      |                                  |                                                                          |
      | `substrate_peers_count 5`        |                                                                          |
      |--------------------------------->|                                                                          |
      |                                  | --------------------------------------------------------------------\    |
      |                                  |-| Save metric value with corresponding time stamp in local database |    |
      |                                  | |-------------------------------------------------------------------|    |
      |                                  |                                         -------------------------------\ |
      |                                  |                                         | Every time user opens graphs |-|
      |                                  |                                         |------------------------------| |
      |                                  |                                                                          |
      |                                  |       GET values of metric `substrate_peers_count` from time-X to time-Y |
      |                                  |<-------------------------------------------------------------------------|
      |                                  |                                                                          |
      |                                  | `substrate_peers_count (1582023828, 5), (1582023847, 4) [...]`           |
      |                                  |------------------------------------------------------------------------->|
      |                                  |                                                                          |
再现图
在你开始之前
在开始之前，请验证以下内容：

通过安装Rust 和 Rust 工具链，您已经为 Substrate 开发配置了环境。
您至少完成了之前的一些教程，包括构建本地区块链和模拟网络。
教程目标
通过完成本教程，您将实现以下目标：

安装 Prometheus 和 Grafana。
配置 Prometheus 以捕获 Substrate 节点的时间序列。
配置 Grafana 以可视化使用 Prometheus 端点收集的节点指标。
安装 Prometheus 和 Grafana
出于测试和演示目的，您应该下载binPrometheus 和 Grafana 的编译程序，而不是自己构建工具或使用 Docker 映像。使用以下链接下载适合您架构的二进制文件。本教程假设您在工作目录中使用已编译的二进制文件。

要安装本教程的工具：

在您的计算机上打开浏览器。
从prometheus 下载为 Prometheus 下载适当的预编译二进制文件。
解压缩下载存档并将其解压缩到工作文件夹中。
```shell
gunzip prometheus-2.35.0.darwin-amd64.tar.gz && tar -xvf prometheus-2.35.0.darwin-amd64.tar
```
导航到Grafana OSS 下载。
为您的架构选择适当的预编译二进制文件。
在您的计算机上打开一个终端外壳并运行适当的命令以安装在您的体系结构上。
启动一个 Substrate 节点
Substrate 公开了一个端点，该端点以 port 上可用的Prometheus 公开格式9615提供指标。您可以使用 更改端口`--prometheus-port <PORT>`并使其能够通过本地主机以外的接口访问`--prometheus-external`。

# Optionally add the `--prometheus-port <PORT>`

```shell
./target/release/node-template --dev
```

配置 Prometheus 以抓取您的 Substrate 节点
在安装 Prometheus 的工作目录中，您将找到一个prometheus.yml配置文件。您可以修改此文件（或创建自定义文件）以将 Prometheus 配置为通过将暴露的端点添加到目标数组来抓取它。如果您修改默认配置文件，这将有所不同：
```yaml
# --snip--

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "substrate_node"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    # Override the global default and scrape targets from this job every 5 seconds.
    # ** NOTE: you want to have this *LESS THAN* the block time in order to ensure
    # ** that you have a data point for every block!
    scrape_interval: 5s

    static_configs:
      - targets: ["localhost:9615"]
```
您希望scrape_interval 少于块时间，以确保每个块都有一个数据点！

`prometheus.yml`现在您可以使用修改后的配置文件启动 Prometheus 实例。

假设您已将二进制文件下载cd到工作目录并运行以下命令：
```shell
# specify a custom config file instead if you made one here:
./prometheus --config.file prometheus.yml
```
让这个过程继续运行。

检查所有 Prometheus 指标
在新终端中，您可以通过运行以下命令对 prometheus 进行快速状态检查：
```shell
curl localhost:9615/metrics
```
此命令应返回类似于以下内容的输出：
```hsell
# HELP substrate_block_height Block height info of the chain
# TYPE substrate_block_height gauge
substrate_block_height{status="best"} 7
substrate_block_height{status="finalized"} 4
# HELP substrate_build_info A metric with a constant '1' value labeled by name, version
# TYPE substrate_build_info gauge
substrate_build_info{name="available-vacation-6791",version="2.0.0-4d97032-x86_64-linux-gnu"} 1
# HELP substrate_database_cache_bytes RocksDB cache size in bytes
# TYPE substrate_database_cache_bytes gauge
substrate_database_cache_bytes 0
# HELP substrate_finality_grandpa_precommits_total Total number of GRANDPA precommits cast locally.
# TYPE substrate_finality_grandpa_precommits_total counter
substrate_finality_grandpa_precommits_total 31
# HELP substrate_finality_grandpa_prevotes_total Total number of GRANDPA prevotes cast locally.
# TYPE substrate_finality_grandpa_prevotes_total counter
substrate_finality_grandpa_prevotes_total 31
#
# --snip--
#
```
或者，您可以在浏览器中打开相同的 URL ( http://localhost:9615/metrics) 以查看所有可用的指标数据。

使用 Grafana 可视化 Prometheus 指标
启动 Grafana 后，您可以在浏览器中导航到它。

打开浏览器并导航到 Grafana 使用的端口。默认情况下，URL 是https://localhost:3000/.
使用默认用户admin和密码登录admin并导航到位于 的数据源页面localhost:3000/datasources。
然后，您需要选择Prometheus数据源类型并指定 Grafana 需要在哪里查找它。

Grafana 需要的 Prometheus 端口不是您在`prometheus.yml`文件 ( http://localhost:9615) 中为节点发布其数据的位置设置的端口。

在同时运行 Substrate 节点和 Prometheus 的情况下，配置 Grafana 以在其默认端口http://localhost:9090或自定义端口上查找 Prometheus。

单击保存并测试以确保您正确设置了数据源。现在您可以配置一个新的仪表板。
模板 Grafana 仪表板
如果你想从这里开始一个基本的仪表板，这里有一个模板示例，你可以Import在 Grafana 中获取有关节点的基本信息：


Grafana 仪表板
如果您想创建自己的仪表板，请参阅Grafana 的 prometheus 文档。

如果您创建自定义仪表板，请考虑将其上传到Grafana 仪表板。公共Substrate 节点模板仪表板可从 Grafana 仪表板下载。您可以通过在Awesome Substrate存储库中列出您的仪表板来让 Substrate 构建器社区知道您的仪表板存在。

下一步去哪里
添加可信节点
监控您的节点
基板普罗米修斯出口商
Polkadot 网络仪表板