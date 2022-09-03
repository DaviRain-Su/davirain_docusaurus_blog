# 状态转换和存储

Substrate 使用一个简单的键值对数据存储，实现为数据库支持的修改后的 Merkle 树。Substrate 的所有高级存储抽象都建立在这个简单的键值存储之上。

键值数据库
Substrate 使用 RocksDB 实现其存储数据库，RocksDB是一种用于快速存储环境的持久键值存储。它还支持实验性Parity DB。

DB 用于所有需要持久存储的 Substrate 组件，例如：

基板客户
基板轻客户端
链下工作者
特里抽象
使用简单键值存储的一个优点是您可以轻松地在其之上抽象存储结构。

Substrate 使用 Base-16 Modified Merkle Patricia 树（“trie”）paritytech/trie来提供 trie 结构，其内容可以修改，并且其根哈希可以有效地重新计算。

尝试允许有效存储和共享历史块状态。trie 根是 trie 中数据的表示；也就是说，具有不同数据的两次尝试将始终具有不同的根。因此，两个区块链节点可以通过简单地比较它们的 trie 根来轻松验证它们是否具有相同的状态。

访问 trie 数据的成本很高。每个读取操作需要 O(log N) 时间，其中 N 是存储在 trie 中的元素数。为了缓解这种情况，我们使用键值缓存。

所有的 trie 节点都存储在数据库中，部分 trie 状态可以被修剪，即当键值对超出非归档节点的修剪范围时，可以从存储中删除。出于性能原因，我们不使用引用计数。

状态树
基于 Substrate 的链有一个主树，称为状态树，其根哈希放置在每个块头中。这用于轻松验证区块链的状态，并为轻客户端验证证明提供依据。

这个 trie 只存储规范链的内容，而不是分叉。有一个单独的state_db层维护 trie 状态，所有非规范的引用都计入内存。

孩子特里
Substrate 还提供了一个 API 来生成具有自己的根哈希的新子尝试，这些根哈希可以在运行时使用。

子 trie 与主状态 trie 相同，只是子 trie 的根被存储和更新为主 trie 中的节点而不是块头。由于它们的标头是主状态树的一部分，因此当它包含子尝试时，仍然很容易验证完整的节点状态。

当您希望自己的独立 trie 具有单独的根哈希，您可以使用它来验证该 trie 中的特定内容时，子尝试很有用。trie 的子部分没有自动满足这些需求的类似根哈希的表示；因此，改为使用子 trie。

查询存储
使用 Substrate 构建的区块链公开了可用于查询运行时存储的远程过程调用 (RPC) 服务器。当您使用 Substrate RPC 访问存储项时，您只需要提供与该项关联的密钥。Substrate 的运行时存储 API公开了许多存储项类型；继续阅读以了解如何计算不同类型存储项目的存储密钥。

存储值键
要计算简单存储值的键，请获取包含存储值的托盘名称的TwoX 128 散列，并将存储值本身名称的 TwoX 128 散列附加到它上面。例如，Sudo托盘公开了一个名为的存储值项Key：

twox_128("Sudo")                   = "0x5c0d1176a568c1f92944340dbfed9e9c"
twox_128("Key")                    = "0x530ebca703c85910e7164cb7d1c9e47b"
twox_128("Sudo") + twox_128("Key") = "0x5c0d1176a568c1f92944340dbfed9e9c530ebca703c85910e7164cb7d1c9e47b"

如果熟悉的Alice帐户是 sudo 用户，读取 Sudo 托盘的存储值的 RPC 请求和响应Key可以表示为：

state_getStorage("0x5c0d1176a568c1f92944340dbfed9e9c530ebca703c85910e7164cb7d1c9e47b") = "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d"

在这种情况下，返回的值 ( "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d") 是 Alice 的SCALE编码的帐户 ID ( 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY)。

您可能已经注意到，非加密的TwoX 128 哈希算法用于生成存储值密钥。这是因为无需支付与加密哈希函数相关的性能成本，因为哈希函数的输入（托盘和存储项目的名称）由运行时开发人员确定，而不是由区块链的潜在恶意用户确定.

存储映射键
与存储值一样，存储映射的键等于托盘名称的 TwoX 128 哈希，其中包含映射到存储映射本身名称的 TwoX 128 哈希之前。要从映射中检索元素，请将所需映射键的哈希附加到存储映射的存储键。对于具有两个键的映射（Storage Double Maps），将第一个映射键的哈希和第二个映射键的哈希附加到 Storage Double Map 的存储键。

与存储值一样，Substrate 对托盘和存储映射名称使用 TwoX 128 散列算法，但在确定元素的散列键时，您需要确保使用正确的散列算法（在#[pallet::storage]宏中声明的算法）在地图中。

这是一个示例，说明查询从以帐户余额命名FreeBalance的托盘命名的存储映射。在本例中，地图使用透明的 Blake2 128 Concat 散列算法：BalancesAliceFreeBalance

twox_128("Balances")                                             = "0xc2261276cc9d1f8598ea4b6a74b15c2f"
twox_128("FreeBalance")                                          = "0x6482b9ade7bc6657aaca787ba1add3b4"
scale_encode("5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY") = "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d"

blake2_128_concat("0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d") = "0xde1e86a9a8c739864cf3cc5ec2bea59fd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d"

state_getStorage("0xc2261276cc9d1f8598ea4b6a74b15c2f6482b9ade7bc6657aaca787ba1add3b4de1e86a9a8c739864cf3cc5ec2bea59fd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d") = "0x0000a0dec5adc9353600000000000000"

从存储查询返回的值（"0x0000a0dec5adc9353600000000000000"在上面的示例中）是 Alice 帐户余额的SCALE编码值（"1000000000000000000000"在此示例中）。请注意，在散列 Alice 的帐户 ID 之前，它必须进行 SCALE 编码。另请注意，blake2_128_concat函数的输出由 32 个十六进制字符组成，后跟函数的输入。这是因为 Blake2 128 Concat 是一种透明的散列算法。

尽管上面的示例可能使这个特性看起来是多余的，但当目标是迭代映射中的键（而不是检索与单个键关联的值）时，它的实用性变得更加明显。为了允许人们以一种看起来很自然的方式（例如 UI）使用地图，迭代地图中的键的能力是一项常见要求：首先，向用户呈现地图中的元素列表，然后，该用户可以选择他们感兴趣的元素并查询地图以获取有关该特定元素的更多详细信息。

这是另一个使用相同示例存储映射的示例（一个名为的映射FreeBalances，它在一个名为的托盘中使用 Blake2 128 Concat 散列算法Balances，演示了使用 Substrate RPC 通过state_getKeysRPC 端点查询存储映射的键列表：

twox_128("Balances")                                      = "0xc2261276cc9d1f8598ea4b6a74b15c2f"
twox_128("FreeBalance")                                   = "0x6482b9ade7bc6657aaca787ba1add3b4"

state_getKeys("0xc2261276cc9d1f8598ea4b6a74b15c2f6482b9ade7bc6657aaca787ba1add3b4") = [
 "0xc2261276cc9d1f8598ea4b6a74b15c2f6482b9ade7bc6657aaca787ba1add3b4de1e86a9a8c739864cf3cc5ec2bea59fd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d",
 "0xc2261276cc9d1f8598ea4b6a74b15c2f6482b9ade7bc6657aaca787ba1add3b432a5935f6edc617ae178fef9eb1e211fbe5ddb1579b72e84524fc29e78609e3caf42e85aa118ebfe0b0ad404b5bdd25f",
 ...
]

Substrate RPC 端点返回的列表中的每个元素state_getKeys都可以直接用作 RPCstate_getStorage端点的输入。事实上，上面示例列表中的第一个元素等于上state_getStorage一个示例中用于查询的输入（用于查找余额的那个）Alice）。因为这些密钥所属的映射使用透明散列算法来生成其密钥，所以可以确定与列表中的第二个元素关联的帐户。请注意，列表中的每个元素都是以相同的 64 个字符开头的十六进制值；这是因为每个列表元素代表同一个映射中的一个键，并且该映射是通过连接两个 TwoX 128 哈希来标识的，每个哈希都是 128 位或 32 个十六进制字符。丢弃列表中第二个元素的这一部分后，剩下0x32a5935f6edc617ae178fef9eb1e211fbe5ddb1579b72e84524fc29e78609e3caf42e85aa118ebfe0b0ad404b5bdd25f.

您在前面的示例中看到，这表示某个SCALE编码的帐户 ID 的 Blake2 128 Concat 哈希。Blake 128 Concat 散列算法包括将散列算法的输入附加（连接）到其 Blake 128 散列。这意味着 Blake2 128 Concat 散列的前 128 位（或 32 个十六进制字符）表示 Blake2 128 散列，其余部分表示传递给 Blake 2 128 散列算法的值。在此示例中，在您删除表示 Blake2 128 哈希的前 32 个十六进制字符（即0x32a5935f6edc617ae178fef9eb1e211f）之后，剩下的是十六进制值0xbe5ddb1579b72e84524fc29e78609e3caf42e85aa118ebfe0b0ad404b5bdd25f，它是一个SCALE编码的帐户 ID。解码此值会产生结果5GNJqTPyNqANBkUVMN1LPPrxXnFouWXoe2wNSmmEoLctxiZY，即熟悉Alice_Stash帐户的帐户 ID。

运行时存储 API
Substrate 的FRAME Support crate提供了用于为运行时的存储项生成唯一的、确定性的密钥的实用程序。这些存储项放置在状态树中，可以通过 key 查询树来访问。

下一步去哪里
运行时存储
类型编码 (SCALE)
