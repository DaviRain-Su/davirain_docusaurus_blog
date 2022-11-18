# Rebase 日报

## 1030

- [逐步学习正则表达式，从零基础到高阶](https://regexlearn.com/zh-cn)
  - Sam: 想脱离枯燥的正则表达式学习吗？可以试试这个工具，它提供了一种游戏化的方式让你可以愉快的学习正则表达式的使用
- [比特币交易的手续费追加方法简介](https://www.btcstudy.org/2022/03/17/introduction-to-fee-bumping-by-Bitcoin-Optech/)
  - Collin: 比特币网络手续费变化可能会导致交易滞留在交易池中无法打包，针对交易池中的交易追加矿工费文章介绍了两种方式：手续费替换（Replace-by-fee，RBF）和子为父偿（Child Pays for Parent，CPFP）钱包开发同学可以参考下。
- [一种sybil攻击检测方法](https://gov.gitcoin.co/t/opendata-community-hackathon-results/11943)
  - Ryan: gitcoin的OpenData黑客马拉松结果中公布了一种sybil攻击检测方法

## 1029

- [独立开发者周刊](https://www.ezindie.com/weekly/issue-65)
  - 昊暠skyh: 独立开发者周刊，经常介绍一些好玩的人或项目，比如这里的RMRK
- [Rust社区生态总结](https://blessed.rs/crates)
  - Mark Tang: Blessed是一个对Rust社区生态的总结，介绍了一些已经成为事实标准的第三方库，让你在选择时有所参考
- [加密钱包纵览](https://research.thetie.io/crypto-wallets-the-drivers-of-mass-adoption/)
  - Rain: The tie这边报告详细地从钱包类型，托管钱包到非托管钱包，OmniChain,C端和B端的业务等一系列主题介绍了crypto wallets这个主题

## 1028

- [介绍 A16Z 的轻客户端 Helios](https://a16zcrypto.com/building-helios-ethereum-light-client/)
  - Qi Zhou: a16z 推出了 helios 的轻客户端，充分利用以太坊 2.0 的 PoS 的技术，能够在 2 秒的时间成为一个以太坊的轻客户端，更好去中心化的方式验证以太坊的数据。
- [On-Chain Analysis of GMX and Perp v2](https://0xatomist.substack.com/p/on-chain-analysis-of-gmx-and-perp)
  - Rex: 对比今年 GMX 和 Perp v2 两个衍生品 DEX 的 Future 部分交易情况。其中 GMX 交易量稳定增长，Perp 波动较大，还包括了两个项目的留存（粘性）、收入及其他核心指标。
- [Khan Academy 期权基础课程](https://www.khanacademy.org/economics-finance-domain/core-finance/derivative-securities)
  - Zhangxuesong: 推荐 Khan Academy 讲解期权的基础课程。 很适合非金融出身的初学者。

## 1027

- [LVR: Quantifying the Cost of Providing Liquidity to Automated Market Makers](https://a16zcrypto.com/lvr-quantifying-the-cost-of-providing-liquidity-to-automated-market-makers/)
  - 李一笑: 本文引入了 LVR 的概念来评估 AMM 做市的成本，通常使用的无常损失(impermanent loss)并没有考虑时间和价格波动率产生的机会成本，使用 IVR 指标可以量化手续费收益和波动率的关系，以选择潜在收益高的池子和做市价格区间。
- [EFF: Code, Speech, and the Tornado Cash Mixer](https://www.eff.org/deeplinks/2022/08/code-speech-and-tornado-cash-mixer)
  - Harry: 帕特尔法官在伯恩斯坦案中解释了为什么第一修正案保护代码，并承认存在：> “计算机语言，特别是高级语言……与德语或法语之间没有任何有意义的区别……就像音乐和数学方程式一样，计算机语言就是这样一种语言，它将信息传达给计算机或那些可以阅读它的人。...源代码就是言论。” 计算机程序的创作和共享受到第一修正案的保护，就像音乐作品、电影或科学实验的创作和表演一样。无论哪种分析，GitHub 都有权继续托管 Tornado Cash 源代码存储库的独立副本。此外，对这个分叉或任何其他内容的改进和其他贡献也是受保护的言论，在任何一种审查标准下，政府都不能在宪法上禁止其发表。
- [李逵还是李鬼？假币安 APP 钓鱼分析](https://mp.weixin.qq.com/s/XAB25lvrCavP-PqPgFXASg)
  - NaNa: 从技术角度复盘币安钓鱼 App 路径，提高警惕。避免财产损失。

## 1026

- [详解 KZG 如何应用于 zk-rollup 以及以太坊 DA 方案](https://mp.weixin.qq.com/s/B6wPelh1U90JaasmQtQCdQ)
  - yan: KZG是零知识证明实践中使用最广泛的多项式承诺方案之一，在本文中将详细讲解零知识证明的必备知识-KZG
- [使用 tsdb 监控价格信号](https://www.influxdata.com/blog/create-bitcoin-buy-sell-alerts-influxdb/)
  - shooter: 时序数据是对实体对象持续观测得到的，带有时间戳的数据序列。例如 CPU 利用率，股票价格，风电企业的风机转速等。时序数据分析可以提供重要的商业价值。例如，在工业互联网领域，时序分析可以通过跟踪设备状态，结合订单情况进行产能匹配，优化供应链，提升设备利用率。物联网领域可以实时监测设备状况，及时阻止意外，还可以通过轨迹分析，实现实体追踪和异常行为检测。
- [PLONK by Hand (Part 1: Setup)](https://research.metastate.dev/plonk-by-hand-part-1/)
  - CyberOrange: 手算Plonk，作者用非常小的数值带领大家直接手算了Plonk的相关运算，有助于理解Plonk原理。

## 1025

- [https://feeddd.org/](https://github.com/feeddd/feeds)
  - Caos: 免费的微信公众号 RSS 订阅工具，支持扩展任意 APP, 目前支持的订阅源在此。
- [刷推工具](https://cryptwi.com)
  - River: 按照 KOL，DATA，Trading，Project 等进行分类，支持中英等多种语言。
- [用链上数据分析FTX三大关键问题](https://www.odaily.news/p/5183054.html)
  - Mark·R: 在FTX雷曼时刻下，我们基于区块链透明数据基础上，从不同视角来看看资金流数据能告诉我们什么。

## 1024

- [开源的 Obsidian 微信读书插件：Obsidian Weread Plugin](https://github.com/zhaohongxuan/obsidian-weread-plugin)
  - shooter: 可用于同步微信读书中书籍元信息、高亮标注，划线感想、书评等，并将这些信息转换为 markdown 格式保存到 Obsidian 的文件夹中。
- [Radicle: Decentralizing the Code Collaboration Stack](https://messari.io/report/radicle-decentralizing-the-code-collaboration-stack)
  - Harry: 一篇介绍去中心化代码协作工具 Radicle 的报告。
- [书籍推荐：《Shape Up》](https://basecamp.com/shapeup)
  - Harry: Basecamp 出的书，讲如何利用六周的循环做产品开发，让产品成形（Shape Up）。

## 1023
  
- [Chopsticks：Substrate 网络测试工具](https://github.com/AcalaNetwork/chopsticks)
  - 陈锡亮: Chopsticks 支持一键 fork 大部分 Substrate 网络，包括平行链以及 Kusama Polkadot，同时支持修改 fork 网络数据以及fake signature，可以方便使用主网环境测试复杂的交易等等
- [快速入门开源](https://up-for-grabs.net/)
  - Amagi: 入门开源爱好者在寻找开源项目做贡献？up for grabs 提供一个不错的开始。它收集了人们觉得有价值的开源项目让你快速入门开源之旅
- [一个zk主题的海外课程](https://zk-learning.org/)
  - Dream: 讲师包含Dan Boneh，Shafi Goldwasser等学术大牛， 明年1月份开课，现在可以填写form报名

## 1021

- [Vitalik 关于对 L2 roadmap 的想法](https://ethereum-magicians.org/t/proposed-milestones-for-rollups-taking-off-training-wheels/11571)
  - Qi Zhou: 虽然以太坊一直希望 L2 能够重用以太坊的安全和抗审查能力，但是某总程度上 L2 还是不可避免的引入第三方的信任，尤其是需要升级 L2 在 L1 的合约的情况下。如何了解这些第三方的信任和更好的降低这些信任的安全要求，Vitalik 给了一个不错的总结。
- [What EIP-4337 Brings: Account Abstraction](https://medium.com/@denniswon/what-eip-4337-brings-account-abstraction-a7ff0572be9)
  - 北美开吻: 这篇文章关于 EIP4337 的账户抽象，不知道有没有分享过，我观察到最近讨论这个话题的特别多，我自己也很感兴趣，所以和大家分享一下.
- [Uniswap V3 ETH/USDC 资金池订单流毒性分析](https://threadreaderapp.com/thread/1589022227437039616.html)
  - Rex: 用高频交易指标 markout 拆解 Uniswap V3 ETH/USDC 池发现 2021 年 8 月以来LP 损失接近 $100M。LPs 本身就是在对赌提供流动性期间产生的手续费收益大于无偿损失，而 2021 年底以来单边下跌的走势本身就会造成 LPs 无偿损失超过手续费收入，并且难以回收的情况。当价格走势为横盘波动且有一定成交量时，LPs 才是一个比较好的策略。

## 1020

- [一个推荐有关区块链的 rust 模块库的网站](https://rustinblockchain.org/)
  - Zhangxuesong: 这个网站推荐一些关于区块链相关的 rust 模块库。不论是学习 rust 还是想寻找解决目前问题的外部模块，都可以尝试一下。
- [一文了解 Layer2 的四大解决方案交易成本对比](https://w3hitchhiker.mirror.xyz/7dwD76ZZIlR7ep731K6y9vTTuXGHOojxWSnkXKzqPzI)
  - 昊暠skyh: 通过代码方式展示 zk-rollup，arbitrum，op 的 gas 机制
- [如何成为链上数据分析师-DUNE入门教程](https://sixdegreelab.gitbook.io/mastering-chain-analytics/00_introductions)
  - 李一笑: DUNE 是链上数据分析最常用的平台，支持使用 SQL 查询多链的链上数据。本教程介绍了 DUNE 的基础使用方法，以及 DUNE 数据引擎各个数据表的说明。

## 1019

- [SQLite3 官方支持 WebAssembly](https://sqlite.org/wasm/doc/ckout/index.md)
  - shooter suter: SQLite 官方的 wasm 项目终于来了！这表示 WebAssembly 在 SQLite 社区完全进入工业级应用阶段，以后就可以网页调用 SQLite 数据库了。在2022 年 9 月，Google 的 Chrome 开发团队宣布与 SQLite 开发团队合作，并开发了 SQLite 的 WebAssembly 版本，作为替代的 Web SQL 数据库 API。
- [POS下的UniswapV3 TWAP预言机风险](https://uniswap.org/blog/uniswap-v3-oracles)
  - twpony: POW转变为POS，POW下每个块的创建者是随机的，而POS下验证者知道自己是否将产出下一个块，块形成机制发生改变给恶意后跑带来可能。UniswapV3使用时间加权的预言机，虽然POS下攻击成本不低，但还是引入了新的风险。本来探讨了相关风险、攻击成本和可能的应对方法。
- [分析EIP-4337：以太坊最强账户抽象提案](https://www.chainfeeds.xyz/feed/detail/7477b86e-fd76-4df3-9443-cfb3204b64e0)
  - NaNa: 文章主述以太坊账户、账户抽象、合约钱包、EIP-4337 工作原理和优缺点。以太坊账户抽象方案 EIP-4337 可以让钱包免助记词及代付 Gas，并且验证和执行逻辑也高度定制化，存在巨大的想象空间。

