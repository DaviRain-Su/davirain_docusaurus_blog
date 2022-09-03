# 远程过程调用

远程过程调用或 RPC 是外部程序（例如浏览器或前端应用程序）与 Substrate 节点通信的一种方式。它们用于检查存储值、提交交易和查询当前的共识权限。Substrate 带有几个默认的 RPC。在许多情况下，将自定义 RPC 添加到您的节点很有用。

RPC 扩展构建器
要将自定义 RPC 客户端连接到 Substrate 节点，您必须提供称为 RPC 扩展构建器的函数。此函数接受节点是否应接受或拒绝不安全的 RPC 调用的参数，并返回IoHandler节点创建 JSON RPC 所需的参数。有关更多上下文，请查看RpcExtensionBuildertrait API文档以了解更多信息。

RPC 类型
RPC 可以是节点共识机制的接口，也可以是与任何外部用户的接口以向区块链提交交易。在所有情况下，重要的是要考虑 RPC 公开的端点。

启动一个节点并运行此命令以查看节点的 RPC API 的完整列表：

curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "rpc_methods"}' http://localhost:9933/

公共 RPC
Substrate 节点提供以下命令行选项，允许您公开公开 RPC 接口：

--ws-external
--rpc-external
--unsafe-ws-external
--unsafe-rpc-external

默认情况下，如果您尝试公开 RPC 接口并同时运行验证器节点，该节点将拒绝启动。这些--unsafe-*标志允许您抑制此安全措施。暴露 RPC 接口可以打开一个巨大的攻击面，必须仔细审查。

有很多 RPC 方法可用于控制节点的行为，但应避免暴露。例如，您不应公开以下 RPC 方法：

author_submitExtrinsic- 允许将交易提交到本地池。
author_insertKey- 允许将私钥插入本地密钥库。
author_rotateKeys- 会话密钥轮换。
author_removeExtrinsic- 从池中删除并禁止外在。
system_addReservedPeer- 添加保留节点。
system_removeReservedPeer- 删除了保留节点。
您还应该避免公开可能需要很长时间才能执行的 RPC 方法，这可能会阻止客户端同步。例如，您应该避免使用以下 RPC 方法：

storage_keys_paged- 获取具有特定前缀和分页支持的状态中的所有键。
state_getPairs- 获取状态中具有特定前缀的所有键及其值。
这些 RPC 是使用#[rpc(name = "rpc_method")]宏声明的，其中rpc_method是函数的名称——例如author_submitExtrinsic对应于submit_extrinsic.

如果请求来自不受信任的用户，则过滤掉此类调用至关重要。做到这一点的方法是通过一个JSON-RPC代理，该代理能够检查调用并只传递允许的 API 集。

remote_externalities 的 RPC
在remote_externalities. 允许您对rpc_apiSubstrate 节点进行一次性 RPC 调用，这对于使用诸如此类try-runtime的工具进行测试很有用。

端点
启动任何 Substrate 节点时，您可以使用以下两个端点：

HTTP 端点：http://localhost:9933/
Websocket端点：ws://localhost:9944/
大多数 Substrate 前端库和工具使用更强大的 WebSocket 端点与区块链交互。通过 WebSockets，您可以订阅链状态，例如事件，并在您的区块链发生变化时接收推送通知。

要调用Metadata端点，请在正在运行的节点旁边运行此命令：

curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "state_getMetadata"}' http://localhost:9933/

此命令的返回值不是人类可读的格式。为此，它需要使用Type encoding (SCALE)。

每个存储项都有一个与之关联的相对存储键，用于查询存储。这就是 RPC 端点知道去哪里寻找的方式。

例子
state_getMetadata
RPC 请求：

function get_metadata_request(endpoint) {
  let request = new Request(endpoint, {
    method: "POST",
    body: JSON.stringify({
      id: 1,
      jsonrpc: "2.0",
      method: "state_getMetadata",
    }),
    headers: { "Content-Type": "application/json" },
  });
  return request;
}

朴素的文本解码：

function decode_metadata(metadata) {
  return new TextDecoder().decode(util.hexToU8a(metadata));
}

state_getStorage
RPC 请求：

Request:   {"id":1,"jsonrpc":"2.0","method":"state_getStorage",["{storage_key}"]}

其中storage_key是由托盘名称、功能和键（可选）生成的参数：

function get_runtime_storage_parameter_with_key(module_name, function_name, key) {
  // We use xxhash 128 for strings the runtime developer can control
  let module_hash = util_crypto.xxhashAsU8a(module_name, 128);
  let function_hash = util_crypto.xxhashAsU8a(function_name, 128);

  // We use blake2 256 for strings the end user can control
  let key_hash = util_crypto.blake2AsU8a(keyToBytes(key));

  // Special syntax to concatenate Uint8Array
  let final_key = new Uint8Array([...module_hash, ...function_hash, ...key_hash]);

  // Return a hex string
  return util.u8aToHex(final_key);
}

下一步去哪里
JSON-RPC 的 Rust 实现
类型编码 (SCALE)
运行时存储