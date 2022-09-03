# 内存分析器
内存分析使您能够了解区块链应用程序在基于 Substrate 的客户端中的内存分配和行为。它在内存分配方式的上下文中识别方法调用，并将此信息与分配的对象数结合起来。此外，分析可用于分析内存泄漏、识别内存消耗发生的位置、定义临时分配以及调查应用程序中的过多内存碎片。

我们推荐的 profiler 是koute 的 memory profiler。

安装
您可以从发布下载预编译的分析器二进制版本。我们测试的最后一个版本是 0.6.1，但任何更新的版本也很可能可以工作。

以下是如何从命令行下载和解压缩它：

$ curl -L https://github.com/koute/memory-profiler/releases/download/0.6.1/memory-profiler-x86_64-unknown-linux-gnu.tgz -o memory-profiler-x86_64-unknown-linux-gnu.tgz
$ tar -xf memory-profiler-x86_64-unknown-linux-gnu.tgz

这将导致三个文件被解压缩。我们只对其中两个感兴趣：

libmemory_profiler.so- 这是我们将挂钩到 Substrate 中的内存分析器本身
memory-profiler-cli- 这是我们稍后将用来分析分析数据的程序
您也可以自己从源代码编译分析器。

首先，您需要确保已安装以下内容：

GCC 工具链
Rust nightly（我们已经测试过版本nightly-2021-06-08）
Yarn包管理器（用于构建 GUI）
然后你应该能够像这样构建分析器：

$ git clone https://github.com/koute/memory-profiler
$ cd memory-profiler
$ cargo build --release -p memory-profiler
$ cargo build --release -p memory-profiler-cli

您会在target/release/libmemory_profiler.so和中找到我们需要的二进制文件target/release/memory-profiler-cli。

从命令行使用分析启动节点
如果您从命令行手动启动 Substrate，那么将分析器连接到它归结为只需设置一些额外的环境变量，然后像往常一样正常启动它。

首先，我们要启用日志记录，告诉分析器它应该在哪里输出它的日志，它应该在哪里收集它的分析数据，并且可以选择告诉它剔除临时分配：

$ export MEMORY_PROFILER_LOG=info
$ export MEMORY_PROFILER_LOGFILE=profiling_%e_%t.log
$ export MEMORY_PROFILER_OUTPUT=profiling_%e_%t.dat

# Optional, depending on what exact aspect you'd like to profile
# and how long you're going to be profiling.
$ export MEMORY_PROFILER_CULL_TEMPORARY_ALLOCATIONS=1

然后我们可以启动带有分析器的 Substrate：

$ LD_PRELOAD=/path/to/libmemory_profiler.so ./target/release/substrate

设置LD_PRELOAD环境变量将指示 Linux 的动态链接器在 Substrate 启动之前将内存分析器注入到 Substrate 中，这允许分析器挂钩到系统的内存分配例程并跟踪 Substrate 所做的每个内存分配。

使用远程分析启动节点
如果您正在远程运行基于 Substrate 的节点，您可能正在使用systemd它来管理它。以下是在这种情况下如何设置分析的方法。

我们假设您已经下载了分析器的预编译二进制文件或从源代码编译它，并且您在当前目录中拥有它。

首先，我们希望将内存分析器复制到全局可访问的位置，并设置一个可以写入日志和收集分析数据的位置。

$ sudo mkdir -p /opt/memory-profiler/bin
$ sudo cp libmemory_profiler.so /opt/memory-profiler/bin/
$ sudo mkdir /opt/memory-profiler/logs
$ sudo chmod 0777 /opt/memory-profiler/logs

然后我们要设置一个包含所有环境变量的文件来配置分析器本身：

$ echo "MEMORY_PROFILER_OUTPUT=/opt/memory-profiler/logs/profiling_%e_%t_%p.dat" | sudo tee /opt/memory-profiler/env
$ echo "MEMORY_PROFILER_LOGFILE=/opt/memory-profiler/logs/profiling_%e_%t_%p.txt" | sudo tee -a /opt/memory-profiler/env
$ echo "MEMORY_PROFILER_LOG=info" | sudo tee -a /opt/memory-profiler/env
$ echo "MEMORY_PROFILER_CULL_TEMPORARY_ALLOCATIONS=1" | sudo tee -a /opt/memory-profiler/env
$ echo "LD_PRELOAD=/opt/memory-profiler/bin/libmemory_profiler.so" | sudo tee -a /opt/memory-profiler/env

现在您要打开systemd节点的单元文件并在该部分中添加以下[Service]内容：

[Service]
EnvironmentFile=/opt/memory-profiler/env
[Service]如果已经存在，请勿添加其他部分；只需添加EnvironmentFile密钥即可。如果您已经拥有一把EnvironmentFile钥匙，请不要更换它；只需添加第二个，systemd将同时应用。

现在你可以重新加载你的systemd守护进程：

$ sudo systemctl daemon-reload

然后重新启动您的服务以开始分析：

$ sudo systemctl restart kusama

剖析数据将在 收集/opt/memory-profiler/logs。如果要禁用内存分析器，只需删除EnvironmentFile已添加到单元文件的密钥，然后再次重新启动服务。

配置分析器
您还可以设置其他环境变量来配置分析器，尽管除了我们已经展示的那些之外，在正常情况下不需要更改它们。

需要额外考虑的一个配置旋钮是MEMORY_PROFILER_CULL_TEMPORARY_ALLOCATIONS，它控制探查器何时收集短期分配。

默认情况下，分析器将收集分析应用程序所做的每个分配。那是很多数据，并且可以达到每秒兆字节的数量级。如果您只想在短时间内进行分析，或者如果您特别关心诊断临时分配，这很好，但是当您想让分析器运行更长时间时就会​​出现问题。

这就是MEMORY_PROFILER_CULL_TEMPORARY_ALLOCATIONS选项的用武之地。当您通过将其设置为1探查器来打开它时，将忽略所有真正短暂的分配，而不是将它们写入磁盘。这显着减少了生成的数据量，通常在每秒千字节的范围内，这使得分析可以一次运行数天。

分析
现在您已经收集了分析数据，您现在可以对其进行分析。

假设你在同一个目录中同时拥有memory-profiler-cli和.dat你收集的文件，你可以为它加载 GUI：

$ ./memory-profiler-cli server *.dat

这可能需要一段时间，具体取决于您的确切硬件以及您尝试加载的数据量。最终，您应该会看到打印出这样的内容：

[2020-05-06T08:59:20Z INFO  cli_core::loader] Loaded data in 315s 820
[2020-05-06T08:59:20Z INFO  actix_server::builder] Starting 8 workers
[2020-05-06T08:59:20Z INFO  actix_server::builder] Starting server on 127.0.0.1:8080
现在您可以打开您的 Web 浏览器并在http://localhost:8080/.


Graphs from the web UI
还有一个 REST API ，您可以访问您希望将数据导出为另一种格式或以编程方式对其进行检查。

杂项提示
始终检查分析器生成的日志并查看是否存在任何日志是一个好WRN主意ERR。
您可能会在探查器的日志中看到以下错误或警告，具体取决于您正在运行的 Linux 发行版：

The perf_event_open syscall failed for PID 0: Operation not permitted (os error 1)`
这通常是无害的；最多这只会在分析时导致更高的 CPU 使用率。您可以通过执行以下操作来避免它：

$ echo "-1" | sudo tee /proc/sys/kernel/perf_event_paranoid

尽管请注意这可能会带来一些安全隐患。看看man perf_event_open更多细节。

在分析期间，必须将整个数据文件加载到内存中。如果您没有足够的 RAM 并且您将尝试加载一个大文件，分析器可能会耗尽内存并崩溃。