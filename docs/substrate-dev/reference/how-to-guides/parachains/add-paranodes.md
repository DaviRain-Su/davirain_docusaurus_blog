# 添加本地平行链节点
正如您在准备本地平行链测试网中所了解的，平行链可以与单个整理者一起使用。

额外的中继链节点
您应该为网络上的每个收集人（平行链块创作节点）运行至少两个验证人（中继链节点） 。

您可以修改提供的普通 中继链规范文件以包含更多验证器，或者采用更“正确”的路线来修改中继链的创世状态源chain_spec.rs，以在创世时添加更多测试网验证器。回想一下如何在添加可信节点教程中生成链规范。

启动整理器
启动收集器节点
有了链规范，您现在可以启动收集器节点。请注意，我们需要在命令的后半部分提供用于启动中继链节点的相同中继链链规范：
```
./target/release/parachain-collator \
--alice \
--collator \
--force-authoring \
--chain rococo-local-parachain-2000-raw.json \
--base-path /tmp/parachain/alice \
--port 40333 \
--ws-port 8844 \
-- \
--execution wasm \
--chain <relay chain raw chain spec> \
--port 30343 \
--ws-port 9977
```
启动第二个整理器
运行附加整理器的命令如下，假设Alice节点已经在运行。此命令与我们用于启动第一个整理器的命令几乎相同，但我们需要避免端口和base-path目录冲突：
```
./target/release/parachain-collator \
--bob \
--collator \
--force-authoring \
--chain rococo-local-parachain-2000-raw.json \
--base-path /tmp/parachain/bob \
--bootnodes <a running collator node> \
--port 40334 \
--ws-port 9946 \
-- \
--execution wasm \
--chain <relay chain chain spec> \
--port 30344 \
--ws-port 9978
--bootnodes <other relay chain node>
```
相关资料
Polkadot 维基：整理者。
准备本地平行链测试网教程。