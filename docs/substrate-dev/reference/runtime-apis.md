# 运行时 API

Substrate 附带以下运行时 API：

- BlockBuilder：提供构建块所需的功能。
- TaggedTransactionQueue：处理验证事务队列中的事务。
- OffchainWorkerApi：处理链下功能。
- AuraApi：使用Aura 共识处理阻止作者身份。
- SessionKeys：生成和解码会话密钥。
- GrandpaApi：将GRANDPA finality gadget 集成到运行时。
- AccountNonceApi：处理查询事务索引。
- TransactionPaymentApi：处理查询交易信息。
- Benchmark：提供一种对FRAME 运行时进行基准测试的方法。

## 学到更多

如何使用impl_runtime_apis宏设计自定义运行时 API