# 发出链下 HTTP 请求

本指南逐步使用链下工作人员发出 HTTP 请求，以获取或发布链下数据。

引入了链下工作者（简称OCW）来扩展 Substrate 区块链的类似预言机的功能。由于区块链无法访问其自身网络之外的数据，因此预言机是实现链上和链下世界之间交互的有用工具。

在本指南中，我们将介绍从cryptocompareAPI 中检索比特币的价格以及通过 OCW API 提交数据。

请记住，尽管 Rust 提供了各种用于发出 HTTP 请求的库，但 OCW 在无标准环境中运行。幸运的是，Substrate 为我们提供了一些no_std库，我们可以使用这些库向 API 发出 HTTP 请求。

Substrate HTTP 库支持以下方法：

得到
邮政
放
修补
删除
设置截止日期并实例化 HTTP 请求
创建 2 秒的最后期限。

我们想保持offchain worker执行时间合理，所以我们将硬编码的deadline设置为2s来完成外部调用。您也可以无限期地等待响应，但是您仍然可能会收到来自主机的超时。
```
let deadline = sp_io::offchain::timestamp().add(Duration::from_millis(2_000));
```
启动外部 HTTP GET 请求。
```
let request = http::Request::get("https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD");
let pending = request.deadline(deadline).send().map_err(|_| http::Error::IoError)?;
let response = pending.try_wait(deadline).map_err(|_| http::Error::DeadlineReached)??;
```
阅读并提交回复
首先，您必须检查响应。
```
// Let's check the status code before we proceed to reading the response.
if response.code != 200 {
 log::warn!("Unexpected status code: {}", response.code);
 return Err(http::Error::Unknown)
}
```
阅读回复。
```
let body = response.body().collect::<Vec<u8>>();
// Create a str slice from the body.
let body_str = sp_std::str::from_utf8(&body).map_err(|_| {
 log::warn!("No UTF8 body");
 http::Error::Unknown
})?;
```
现在，您可以通过 POST 请求向 API 提交数据。
```
// Send a POST request
let request_body = Vec::new();
let request = http::Request::post("https://reqres.in/api/users", vec![request_body.clone()])
 .add_header("x-api-key", "test_api_key")
 .add_header("content-type", "application/json");

let pending = request
 .deadline(deadline)
 .body(vec![request_body.clone()])
 .send()
 .map_err(|_| http::Error::IoError)?;

// Wait for response
let response = pending
 .try_wait(deadline)
 .map_err(|_| http::Error::DeadlineReached)??;
```
例子
托盘示例：链下工人
演示：OCW 托盘
来源：基板核心原语