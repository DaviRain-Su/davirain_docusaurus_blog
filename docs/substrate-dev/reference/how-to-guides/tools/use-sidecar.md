# 使用 REST 端点获取链数据
本操作指南说明了使用sidecar服务提供的 REST 端点与 Substrate 区块链节点进行交互。

要找到已完成拍卖的获胜者，我们需要知道拍卖结束的区块号。由于Sidecar是一个无状态的 API，并且拍卖信息存储在拍卖的最后一个区块中，一旦拍卖结束，我们需要区块号来进行历史查询，以检索存储在其中的事件和数据。

目标
使用以下方法查找已完成的平行链拍卖的获胜者sidecar

用例
使用 REST 服务与 Substrate 区块链节点交互。

脚步
1. 利用 /experimental/paras/auctions/current 端点
我们将在数据库中跟踪和存储finishEnd、auctionIndex和leasePeriods：

finishEnd: 这是拍卖的最后一个区块。存储它可以让您查询拍卖结束的区块。从该块中，您可以提取与赢得租赁相关的事件。（查询区块：GET /blocks/{finishEnd}。）
auctionIndex：拍卖的唯一标识符。
leasePeriods：可以为特定的拍卖指数投标的可用租赁期指数。
2. 使用 Sidecar 寻找拍卖赢家
通过存储finishEnd区块并查看其中的Leased事件，我们可以看到谁是拍卖赢家以及他们获得了哪些租赁期。

然而，格式化数据是必要的，例如：

auctionIndex: {
    leasePeriods: [
        "11", "12", "13", "14"
    ],
    finishEnd: '200'
}

3. 查询 /blocks/:blockId 端点
finishEnd此步骤查询字段中指定的块高度处的所有块，并检索on_initialize. 一个示例响应是：

{
    authorId: ....,
    extrinsics:....
    ...
    on_initialize: {
        events: [
            {
                "method": {
                    "pallet": "slots",
                    "method": "Leased"
                },
                "data": [
                    '1000', // ParaId
                    '5HpG9w8EBLe5XCrbczpwq5TSXvedjrBGCwqxK1iQ7qUsSWFc', // AccountId
                    '1', // LeasePeriod (begining of the lease period)
                    '4', // LeasePeriod (the count of the lease period)
                    '10000', // Balance (extra balance reserved)
                    '1000000000', // Balance (total balance)
                ]
            },
            {
                "method": {
                    "pallet": "auctions",
                    "method": "AuctionClosed"
                },
                "data": [
                    ...
                ]
            }
        ]
    }
}

4. 比较您的数据
现在您已经paraIds获得了该拍卖的所有赢得位置，您可以将其与与auctionIndex. 将leasePeriods在活动拍卖期间可用的与在活动中leasePeriods赢得并表示的 Leased（可能有多个，如果有多个获胜者）进行比较，将为您提供该拍卖的所有获胜者。

例子
更多指南在这里
资源
边车文档
可用的 Substrate Sidecar API 端点