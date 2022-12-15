# Rebase 日报

## 1031 

- [解读zkRollup扩容方案Scroll的架构设计](https://www.panewslab.com/zh/articledetails/tjkbt654.html )
  - Mark·R: 被V神亲手点赞的项目，让我们拆解下它架构上做了哪些创新
- [Bootstrapping your personal Web3 info hub from more than 600 RSS Feeds.](https://github.com/chainfeeds/RSSAggregatorforWeb3 )
  - OxMax: 优质的web3 rss流聚合
- [34 个全平台免费访问的 Web3 数据网站](https://mp.weixin.qq.com/s/6taXkS14Tk-sdFyX-wg7IQ )
  - Sun: 这里很多免费数据工具，有助于大家多方位了解链上动态。

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

## 1018

- [快速AI generate art，stable diffusion](https://stableboost.ai/)
  - Survivor: 迄今最快的stable diffusion，一次200张，超量后价格巨便宜，记得切换到非国内ip，用谷歌帐号直接登录即可
- [命令行速查工具](http://cheat.sh)
  - Caos: 可以用 curl 直接搜各种命令和语法，同时还支持 stackoverflow 搜索。仓库地址: https://github.com/chubin/cheat.sh
- [Discord 服务器搭建系列教程](https://www.youtube.com/watch?v=WNbcpsiFEq4&list=PLcHEApQsRODU5jGvudRl3AFbhGiOtqARl)
  - River: Discord搭建服务器的系列教程，包含简单应用和各种免费机器人的使用等。

## 1017

- [Zksync测试网的交互教程](https://medium.com/@CalendarDefi/testnet-guide-zksync-2-0-6fabedf44f21)
  - Sun: ZKsync最近即将有大新闻发布，在那之前要不要在上面测试下？
- [一份报告带你走进中国 2000 万开发者](https://www.infoq.cn/minibook/JF1iyU2U7eSg0zENZzhz)
  - Caos: 极客邦科技双数研究院权威出版《中国开发者画像洞察报告2022》，为你深度解读开发者人群背景，分析开发者群体面临的挑战，洞察开发者人群特征，预测开发者生态发展趋势
- [Optimism关于Rollup As A Service的想法](https://optimism.mirror.xyz/fLk5UGjZDiXFuvQh6R_HscMQuuY9ABYNF7PI76-qJYs)
  - 0xMax: Optimism在逐渐开放构建一个Rollup所需要的stack的组件，未来Rollup As A Service可能会成为一个加速eth的多链，多Rollup互操作生态
  
## 1016

- [Prusi: prototype verifier for Rust](https://github.com/viperproject/prusti-dev)
  - 陈锡亮: Prusti 是一个 Rust 正确性检查器，可以分析 Rust 代码，可以检测代码是否正确，比如是否有溢出的可能性.
- [DeFi Hacks Analysis - 漏洞根本原因分析](https://web3sec.notion.site/web3sec/8e9274d6dbbc4ff18d9034179a173fca?v=7ea2c3edd2514c7db80fa61db7571291)
  - Sam: 作者罗列了目前出现的112次安全事故，包括原因概述和重现方式，可以让开发人员进行快速学习，避免再次踩坑
- [深入研究 zkEVM 的五种类型的优缺点](https://mp.weixin.qq.com/s/ShQ25BguauJijBKD_Qh54w)
  - Collin: 本文的重点研究 zkEVM相关的两个关键概念零知识 Rollup (zk-Rollups) 和以太坊虚拟机 (EVM)和以及zkEVM 领域中需要关注的关键项目。

## 1014

- [我们离普及智能合约钱包还有多远？一文看懂ERC-4337](https://mp.weixin.qq.com/s/ZQAVSPHmRCGwtniuItT57w)
  - Qi Zhou: 这篇文章回顾了 EIP-4337 的最新进展，包括为什么需要 4337，4337 有什么新的功能，钱包会有什么样的变化等等有意思的话题。
- [Ethereum post-merge data tools](https://inflectionvc.notion.site/Ethereum-post-merge-data-tools-66a29627f39d474ebc8c55b91f0e1785)
  - Rex: 提供了 Ethereum Merge 后的各类数据、看板、分析工具，包括且不限于 Validator、Builder、MEV (Relayers)、Gas 等等。
- [杀死一个套利机器人](https://media.dedaub.com/killing-a-bad-arbitrage-bot-f29e7e808c7d)
  - Lucas: 该文是 Dedaub 团队提供的一篇对套利合约的逆向分析和安全性评估文章，十分有助于学习套利机器人相关知识。

## 1013

- [一个分析 EVM 交易记录的 Chrome 插件](https://medium.com/@eigenphi/eigentx-chrome-extension-makes-your-defi-experience-better-fb7e3e098f0f)
  - 李一笑: 由 eigenphi 开发的插件，图形化分析 EVM 链上交易记录，支持 Mint,burn, flash loan, liquidation，arbitrage, sandwith  等多种交易类型。支持Ethereum, BSC, Fantom, Avalanche, Polygon 链。
- [DevCon 6 分享: Building a Thriving Developer Community](https://youtu.be/HBczWOD0trg)
  - Harry: 在 Bogota DevCon 上，Superfluid 的 lead DevX Sam Flamini 分享了如何构建一个繁荣的社区，适合做协议层项目和开发者社区的人学习下。下面分享几个要点：1. 你的技术成功的标志是人们愿意基于你的技术构建新的应用，或者愿意和你的技术做集成。2. 开发者关系看起来像 marketing，但绝对不能让人感觉是 marketing。3. 文档是你最有价值的 DevX 产品。把每一次开发者的问题视为文档的失败，因为他们没有从文档中找到答案。
- [通过单个 Slot 实现最终确定性](https://mp.weixin.qq.com/s/NALos4FcMQQNr0jpu8RT3A)
  - sny: 这篇文章回顾了之前的以太坊设计，探讨了把最终确定性时间缩短为一个 slot 的路线。

## 1012

- [比webpack快700倍的新打包器 - turbopack](https://vercel.com/blog/turbopack)
  - Aaron Chi: vercel在招揽webpack创建者之后推出的前端框架，个人认为vercel的产品真的是前端领域的顶流。
- [Layer3 是什么？一文揭晓 Layer1 到 Layer3 进化史](https://mp.weixin.qq.com/s/k71zYci2EgxxKxqOKTuWAw)
  - YAN: 该文将从 Layer1 及Layer2 层面解析Layer3诞生的必要性和实际意义。
- [On the Move — A Critical Analysis of Aptos](https://www.chainfeeds.xyz/feed/detail/2172c2fb-3fa6-4f16-adb1-f8111f32b1a9)
  - NaNa: 该篇文章分析了 Move 语言和 Aptos 自身的技术特征与创新，本文作者认为，Aptos 作为目前第一个也是唯一一个已经上线主网的 Move 语言公链，其技术优势主要还是靠 Move 语言本身的特点实现，如果不能诞生一个 Aptos 链上的现象级应用，其先发优势未来可能会被其他 Move 公链取代。
  
## 1011

- [Halo2学习小组的资料汇总](https://0xparc.notion.site/Halo2-Learning-Group-Homespace-32b6c45eeaa84c3baa01015da98f3ab4)
  - CyberOrange: 由0xParc和Scroll等组织的Halo2学习小组的资料汇总，包括PPT，录播视频等。
- [全面解读流动性资金池：如何成为DeFi的基础组件](https://www.panewslab.com/zh/articledetails/l4gn3qqc.html)
  - Mark·R: 上一轮牛市让DeFi这个词变得高频，而DeFi的关键基础组件流动资金池的原理和如何运作将在文章中回答。   
- [解读 Web3 的意识形态：密码学、分布式存储与博弈论](https://www.8btc.com/article/6784710)
  - River: 文章从意识形态的角度对web3项目进行“竖向”介绍，并阐述Web3的三大基础原则。

## 1010

- [postgres 性能调优](https://luis-sena.medium.com/tuning-your-postgresql-for-high-performance-5580abed193d)
  - 0xMax: 随着postgres应用越来越广泛，推荐一篇做pg性能优化的文章，内容简洁实用
- [ENPUZ —— 在线英语句子语法分析](http://enpuz.com)
  - shooter: Enpuz 全称 English Puzzle，提供众多英语学习工具。最具特色的是基于 AI 的英语长句可视化语法分析。
- [模块化结构下 EIP-4844 的设计逻辑](https://mp.weixin.qq.com/s/ggg888GPDyDpITCwp1v_UQ)
  - Jasmyn: 据以太坊开发路线图，以太坊在合并后的下一个重要阶段是 The Surge，围绕以 Rollup 为中心的路线图开发，在继承以太坊网络安全性的同时，进一步提高 L2 Rollup 的可扩展性。本文主要介绍这一技术路线图中的关键工作：EIP-4844 Proto-dankshading，它如何使得 Rollup 所需要使用的数据变得更加便宜，以及获得更多存储数据的容量？

## 1009

- [一个移动端友好的以太坊区块浏览器](https://www.solidlint.com/)
  - Caos: 一个移动端友好的以太坊区块浏览器, 方便在移动端阅读合约源码和函数, 调用合约的方法还在开发中, 可以保持关注.
- [低门槛钱包：谁能抢占Web3用户第一入口？](https://www.panewslab.com/zh/articledetails/cnv76pki.html)
  - MarkR: 淘金时最赚钱的是卖水的，在web3高速发展期，低门槛钱包很有潜力成长为超级dapp
- [谷歌字体寻找工具](https://google-webfonts-helper.herokuapp.com/)
  - Amagi: 谷歌字体寻找工具，提高字体寻找与使用的效率

## 1007

- [Zero Gas Wasted: Winning the IAmTheOptimizor Challenge](https://a16zcrypto.com/winning-the-iamtheoptimizor-challenge/)
  - Qi Zhou: a16z 参与了一个 gas 的优化项目，通过各种脑洞的操作来降低解决 3sum 问题的 gas 开销，并且进一步证明他们的方案是最优 gas 的。有感兴趣挑战的小伙伴可以去试一试。
- [stablecoins.wtf](https://stablecoins.wtf/ )
  - Rex: 稳定币学习和跟踪工具，展示各个稳定币核心指标、最新新闻以及新兴稳定币分析报告。
- [n0-computer / iroh](https://github.com/n0-computer/iroh )
  - CyberOrange: 用 Rust 实现的下一代 IPFS 客户端，兼顾数据中心、单节点和手机等多种模式，代码结构应该会更合理，性能也更好，但仍处于早期，对 IPFS 感兴趣的可以保持关注。

## 1006

- [Web3 账户概念梳理](https://m.techflowpost.com/article/1495 )
  - sny: 刚刚结束的 Devcon 上，账户抽象算是是最热的几个话题之一。
- [JoyID：CKB 上利用 Webauthn 技术的非托管钱包方](https://talk.nervos.org/t/joyid/6780 )
  - Harry: JoyID 是基于 FIDO Webauthn 协议和 Nervos CKB 的一个跨平台、跨终端、无密码无助记词的钱包方案，用户在创建、使用、甚至恢复时均不需要密码和助记词。JoyID 支持 Webauthn 的主流签名算法 secp256r1（P256）进行签名验证，并在 CKB L1 上使用 CoTA 扩展对用户多个设备生成的公钥进行登记，指向相同的 CoTA Cell ID，从而完成了用户地址的抽象。
- [MPC钱包 vs 智能合约钱包](https://mp.weixin.qq.com/s/FjQcI-u7tmeixqMujKey3A )
  - 昊暠skyh: 笔者认为，智能合约钱包是我们的最终愿景，MPC是短期内更可行的方案，且在一些特殊场景更具优势。因为成本问题，未来智能钱包在以太坊主网实现的可行性和可能性都较低。

## 1005

- [Mud.dev — 链游框架](https://mud.dev/)
  - Survivor: 概述：使用产品的自动组件来映射了链上和客户端，并提供了世界全局同步，包括所有数据（英雄，武器，道具等）和所有系统游戏逻辑，已经基于这个开发了三个示例，让你的链游快速展开。
- [通过空投网络钓鱼](https://forta.org/blog/breaking-the-ice-a-phishing-deep-dive/)
  - twpony: 在Web3中，用户经常被诱骗签署交易，从而让攻击者可以在不知道账户私钥的情况下控制用户的token。本文讨论了几个通过空投（Multi/TrustPad Airdrop Phishing）进行网络钓鱼的案例。
- [引介｜EVM 深入探讨 Part 2](https://mp.weixin.qq.com/s/fif_-19KZUY3ccWFaqw_4g)
  - NaNa: 该系列由慢雾科技团队引介翻译 noxx 的文章（https://noxx.substack.com/）深入探讨 EVM 的基础知识，本文主讲智能合约的内存以及它在 EVM 上的工作方式。

## 1004 

- [推特活动抽奖工具](https://twitterpicker.com )
  - Sam: 此工具可以设置中奖人数，必须follow的账号，还能排除一些僵尸或活跃度很低的账号，方便在推特做抽奖活动。
- [speed run Ethereum ](https://speedrunethereum.com/ )
  - River:  7个 ETH 开发相关教程，包含 NFT/DEX/Oracle 等。
- [Condor Systems Bounty Task](https://galxe.com/CondorSystems )
  - River: 包含 11 个 Condor 开发相关 Bounty 任务.

## 1003

- [MultiAvatar：多元风格头像](https://multiavatar.com)
  - Frozen: MultiAvatar可以生成多元风格的头像，有多达120亿个组合可能，比较方便在产品中进行集成，类似的项目有 ethereum-blockies-base64，基于以太地址生成头像。
- [天王级项目Sui测试交互教程](https://mirror.xyz/meta-mate.eth/AJQLs6a0Yru0mqHWdW-MIbw_jdHzC9plih8Hl7EDTdg )
  - Mark·R: Mark: Aptos的毛相信大家都撸到了吧(逃，没撸到的看这里，这里是另一个同样使用Move提供高吞吐低延迟的下一代智能合约平台交互教程：
- [cex交易API库ccxt](https://github.com/ccxt/ccxt)
  - 0xMax: 以统一的API提供几十个cex的接口，提供JavaScript，PHP，python，快速接交易所数据非常方便。

## 1002

- [自学计算机科学](https://github.com/izackwu/TeachYourselfCS-CN/blob/master/TeachYourselfCS-CN.md)
  - Caos: 自学计算机科学课程, 系统全面的自学教程中英文双版. 英文版在此 https://teachyourselfcs.com
- [Bitcoin Transaction Malleability and MtGox](https://www.cnblogs.com/Y-Wen/p/14752977.html)
  - Collin: 文章对bitcoin交易延展性详细介绍，对于想了解交易延展性问题的同学推荐阅读
- [OSSU Open Source Society University](https://github.com/ossu/computer-science)
  - Amagi: OSSU 是一个收集了科班学习路径与对应课程的项目，所有课程都是以线上学习为主。让你疫情在家就能学CS！

## 1001

- [使用 Go 和 Web 技术开发桌面端应用](https://wails.io)
  - Mark: Wails让你用GO和Web技术来开发桌面端应用，它通过WebKit运行前端窗口，使开发者可以使用任意外部前端技术开发界面，同时Wails在前端程序中植入 Go binding代码，使得前端程序可以远程调用Go代码
- [Latest TEA Project Developments for Epoch 11](https://teaproject.medium.com/latest-tea-project-developments-for-epoch-11-ee3fa98381f3)
  - 北美开吻: 与众不同的云计算经济学设计, 让提供的Host矿工和开发App的Dev互相decouple.矿工挣host的硬件租用费由最终用户支付gas. 软件开发者挣的是软件的使用费用, 使用in-app purchase的方式来支付gas. 这两种gas互不影响. 这样就形成了开发者无需负责和负担云服务的费用, 也无权控制矿工是否愿意host. 矿工成为独立的运营个体,自行担负盈亏. 公共服务采用哈勃格税的方式避免中心化. 值得一读 
- [使用UniswapV3构建期权策略](https://threadreaderapp.com/thread/1581636784693596160.html)
  - 李一笑: 作者Guillaume Lambert是前康奈尔大学物理系教授，目前全职研究基于UniswapV3的永续期权项目-@Panoptic_xyz。本系列推文参考了Panoptic的部分实现，用UniswapV3模拟Reverse Jade Lizard策略。

## 1000

- [基于 IPFS 搭建的去中心化前端 Dappnet](https://twitter.com/liamzebedee/status/1578127982173908992 )
  - Qizhou: 只需要在浏览器输入 vitalik.eth 就可以去中心化访问 vitalik 的网站？Dappnet 给了一些新的解决方案的思路。
- [Centralization, Decentralization, and Internet Standards](https://mnot.github.io/avoiding-internet-centralization/draft-nottingham-avoiding-internet-centralization.html#name-when-centralization-is-unde )
  - Rex: 在政府、公司和非营利组织领域，中心化形式都很好地承担了其职能，为什么互联网领域需要去中心化的形式？中心化和去中心化的核心差异在哪？互联网领域去中心化的可行方向有哪些？
- [BlockScope：检测分析分叉区块链项目中传播的漏洞](https://arxiv.org/abs/2208.00205 )
  - Lucas: 该论文实现了一种通过其新创的代码相似匹配技术对 fork 项目下没有及时修复的漏洞进行漏洞挖掘的技术。具体来说，BlockScope 采用基于相似度的代码匹配技术，设计了一种计算代码相似度的新方法，以涵盖所有语法范围的变体（即 Type-1、Type-2 和 Type-3）。此外，BlockScope 会自动提取和利用补丁代码的上下文来缩小搜索范围并仅定位可能相关的代码以进行比较。

## 998

- [IPFS漏洞分析](https://consensys.net/diligence/blog/2022/09/the-forgotten-ipfs-vulnerabilities/?utm_source=substack&utm_medium=email)
  - twpony: IPFS是一种内容寻址机制，该文章详细讲述了8种IPFS漏洞，并且针对漏洞所提出的解决方案。
- [深入解析四大跨链桥类别：该如何权衡？](https://medium.com/taipei-ethereum-meetup/%E8%B7%A8%E9%8F%88%E6%A9%8B%E6%AF%94%E8%BC%83-4327192f7200)
  - Aaron Chi: 文章归类了几个跨链桥的类别，对一些著名的跨链桥攻击事件进行了分析。
- [以太坊交易查看器](https://github.com/samczsun/ethereum-transaction-viewer-frontend)
  - Zhangxuesong: paradigm 研究员 samczsun 开发的一款以太坊及以太坊兼容链的交易查看器，可以点击交易前面的 [call] 查看详细的交易参数解析。

## 996

- [慢雾：BNB CrossChain Bridge 被黑简析](https://mp.weixin.qq.com/s/sZQp1NPQbFFAwlFjURuUbw )
  - Joe: 内容相当精彩,需要一定的知识储备,感兴趣的可以了解下
- [一份送给 SQL 初学者的简易课程](https://sqlcrashcourse.com/lessons/ )
  - shooter: SQL 是非常强大的数据分析工具。掌握了 SQL，使用起 Dune.com、bigquery 更会得心应手。
- [eth2 validator的硬件开销分析](https://www.symphonious.net/2022/04/06/exploring-eth2-cost-of-adding-validators/ )
  - https://www.symphonious.net/2022/04/06/exploring-eth2-cost-of-adding-validators/ 

## 995

- [Iced: 基于The Elm Architecture的Rust跨平台GUI库](https://github.com/iced-rs/iced)
  - 陈锡亮: Iced是又一个全新的 Rust 跨平台 GUI 库。和其他 Rust 跨平台 GUI 库的主要区别是 Iced 实现了The Elm Architecture，为 GUI 状态和更新和渲染做了一个明确的分层，用于将复杂的渲染逻辑抽象和模块化。
- [ShareMouse - 一套键盘鼠标控制两台电脑](https://www.apprcn.com/sharemouse.html)
  - Collin: ShareMouse可以让你用一套键盘鼠标控制多台电脑，非常适合从事多屏处理的朋友。
- [VS code主题大全](https://vscodethemes.com/)
  - Caos: VSC 主题在线预览, 一览无余,再也不用一个一个试了。

## 993

- [NodeJS Wiki 框架](https://js.wiki)
  - Frozen： 使用 js.wiki 可以快速搭建产品 wiki，方便进行团队内部、产品知识管理。
- [Rust 原生 Fuzz 工具](https://rust-fuzz.github.io/book/cargo-fuzz.html )
  - Lucas： 使用 cargo-fuzz 进行模糊测试，cargo-fuzz 是对 Rust 代码进行模糊测试的官方工具。该工具曾帮助白帽发现 solana 高额悬赏漏洞，是 rust 漏洞挖掘的利器
- [STARK Math Links](https://twitter.com/EliBenSasson/status/1578380154476208131 )
  - Rex： StarkWare Co-founder Eli Ben-Sasson 经常被问关于 STARK 的数学问题，他由浅入深梳理了一系列学习资料。

## 992 

- [使用delegate.cash安全领取空投](https://0xfoobar.substack.com/p/delegatecash)
  - 李一笑： 本文介绍了一个开源的空投领取工具。delegate.cash是一个开源的代理合约，用户可以将有价值的NFT(或者ERC20资产）保存在冷钱包，委托delegate.cash领取NFT可获得的空投代币。合约已经在Ethereum+Polygon+Goerli部署。
- [Aztec 推出了隐私编程语言——Noir](https://github.com/noir-lang/noir)
  - shooter： Noir 是一种用于创建和验证证明的 DSL。它的设计在很大程度上受到 Rust 的影响。Noir 在设计上要简单和灵活得多，它会编译成一种中间语言，很类似 LLVM IR，将编程语言与后端完全解耦。相比 Circom，我们又多一个选择。
- [Celestia 数据可用性介绍](https://mirror.xyz/infinet.eth/ko_swBzclRa5WStYNuSGzB3m2wn2eJlls2tXSdOx5r4)
  - Harry： 数据可用性是指通过数据抽样，保证数据在网络上的可用性。这篇文章介绍了 Celestia 工作流程、与Danksharding对比等内容。

## 991

- [TinyWoW](https://tinywow.com/)
  - Zhangxuesong： 推荐一个较全的免费文件处理工具 - TinyWoW, 可在线处理文档格式转换、PDF 操作编辑, 视频转换以及各类视频、图片、文档的压缩，简单的抠图也可处理。
- [2022年以太坊生态赞助的学术研究课题汇总](https://blog.ethereum.org/2022/07/29/academic-grants-grantee-announce)
  - Survivor： 一个生态发展不仅需要技术，更需要不断的沉淀积累和研究，在经济学、共识层、P2P网络、MEV、形式验证、ZK等领域，每年都有几百万美元的研究课题资助！包括独立研究者占比非常多！
- [solidity的魔法](https://saxenism.com/web3/solidity/language-tricks/bit-magic/intermediate/2022/09/06/Bit-Magic-Solidity.html#get-first-n-bits)
  - 昊暠skyh： 每天学习一点省Gas小技巧。

## 990

- [Market Structure & Design with Guillaume Lambert](https://www.youtube.com/watch?v=vlPIFYfG0FU)
  - Koi：主要讲了怎么在uniswap v3上做永续期权，怎么基于lp来买卖option。由浅入深，对于理解去中心化期权的帮助很大。
- [5亿美元BNB失窃的全过程](https://www.panewslab.com/zh/articledetails/tds9tb6b.html)
  - MarkR：国庆的周末让人加班的突发事件，CZ同时达成了了让网络暂停成就：）
- [AI抠图](https://www.remove.bg)
  - Sam：凭借AI技术，可轻松快速的去除图片背景，提取你想要的人物、产品、动物等等对象。

## 989
  
- [ezcv.pro: 免费在线简历模板生成器](https://www.ezcv.pro/builder)
  - Caos： 在线简历生成工具, 可以自定义布局和字体样式,支持导出 PDF ,求职必备
- [penbot - 一个开源的原型与设计平台](https://github.com/penpot/penpot)
  - Amagi： penbot，面向跨领域团队的开源设计和原型平台，web based，不依赖操作系统，使用开放标准SVG
- [code996 - commit时间分析工具，帮你识别996公司](https://github.com/hellodigua/code996)
  - Caos： 统计 Git 项目的 commit 时间分布，进而推导出项目的编码工作强度，详细介绍在此 https://hellodigua.github.io/code996/#/

## 987

- [metamask 资产看板简介版](https://portfolio.metamask.io/ )
  - Zhangxuesong： metamask 推出了一个简洁的资产看板。demo 展示的是 vitalik.eth 的资产列表。
- [以太坊源码解读](https://github.com/blockchainGuide/blockchainguide/tree/main/source_code_analysis/ethereum/ )
  - Lucas： 该库中有大量对以太坊源码解读的文章，有助于区块链初学者深入理解以太坊的底层框架以及源码逻辑。
- [我们离普及智能合约钱包还有多远？一文看懂ERC-4337](https://mp.weixin.qq.com/s/ZQAVSPHmRCGwtniuItT57w)
  - Rex： 本文介绍智能合约钱包相对 EOA 钱包的优势，相应的技术原理。并详解了新 ERC-4337 钱包给智能合约钱包带来的创新点有哪些，目前 ERC-4337 钱包的进展如何。

## 986

- [一文读懂以太坊新升级方案Danksharding](https://mirror.xyz/mtyl.eth/TbLbRI1VcDZYxkHJOBhB319oaYxnV5_DmqXl6xLfcWM )
  - Harry： Danksharding于2021年末被Dankrad提出，Danksharding帮助以太坊实现了“中心化出块 + 去中心化验证 + 抗审查性”，并将以太坊打造成了结算层与数据可用性层，为L2的计算性能提升留下空间。Danksharding的核心设计：通过RS编码和KZG承诺，解决了数据可用性问题，实现了网络验证的去中心化；通过出块者-打包者分离（PBS），解决MEV问题；通过抗审查清单（Crlist），解决了抗审查问题。
- [如何面对审查问题](https://joncharbonneau.substack.com/p/censorship-wat-do )
  - sny： 作者针对以太坊现在所面临的审查问题, 从开发者, 节点, 协议, MEV 的角度提出了应对方案
- [Zero Knowledge Canon, part 1 & 2](https://a16zcrypto.com/zero-knowledge-canon/?utm_source=substack&utm_medium=email )
  - Koi： A16z出了zk canon，里面整理了zk相关的各种reading，广而全。

## 985

- [Amber Group - 破解 Profanity 漏洞](https://medium.com/amber-group/exploiting-the-profanity-flaw-e986576de7ab)
  - Aaron Chi： 本文详细阐述了破解 Profanity 生成地址的硬件和时间要求，Amber Group 使用 16 GB 内存的 Macbook M1，在不到 10 小时内对数据集进行了预计算，并在不到 48 小时内破解了 0x0000000fe6a514a32abdcdfcc076c85243de899b 的私钥。
- [罗伯特议事规则](https://robertsrules.com/)
  - shooter suter： 《罗伯特议事规则》由亨利·马丁·罗伯特撰写，1876年出版，几经修改后已经发行了第12版。《罗伯特议事规则》的内容非常详细，有专门讲主持会议的主席的规则，有针对会议秘书的规则，有针对不同意见的提出和表达的规则，有辩论的规则，还有不同情况下的表决规则等。其核心原则包括：平衡、对领袖权力的制约、多数原则、辩论原则等，既保障了民主，也保障了效率。
- [新版以太坊白皮书](https://ethereum.org/zh/whitepaper/)
  - Zhangxuesong： 2022年9月30日以太坊官网更新了最新版本以太坊白皮书，在该文中可找到关于以太坊合并后最新的技术信息。

## 984

- [Ethereum Consensus: LMD-GHOST](https://twitter.com/salomoncrypto/status/1576016595452731394?s=46&t=zbiqH2DCH-8p5a_gmgtqmg)
  - CyberOrange：Haym描述了以太坊使用的链分叉选择机制。
- [NFTFi终极指南](https://www.jinse.com/blockchain/2391578.html)
  - River：围绕NFT的金融化出现了一个充满活力和创新的子市场，称为NFTFi（非同质代币金融）。
- [AI Art是下一个NFT？](https://www.8btc.com/article/6780034)
  - River：AI Art今年以来的热度，是因为一种呈现为文字转图像（Text-to-image）特性的崭新交互方式，正在向大众宣告AI Art正在进入一个“民主化”的时代。使用文字描述，或者基于画面意象和故事，或者基于艺术家风格、构图、色彩、透视方法等专业名词，就能在数十秒内生成完整的绘画作品，这让艺术创作成为了一件像跑步一样的事：人人都会跑步，只不过是专业的人跑得更快。

## 983

- [手把手教你参加 OP 版奥德赛](https://www.odaily.news/p/5181873.html )
  - Mark·R： 之前另外一条侧链的奥德赛不知道大家还有没有印象，它因为升级而暂停，现在，OP的奥德赛开启，略熟悉但又增加了答题环节，时间轴较长，想玩的玩家注意控制好风险，祝玩得愉快：）
- [Chainlink 2.0 全面解析](https://www.odaily.news/p/5182084.html )
  - Mark·R： 在DeFi时代的各种协议都离不开Chainlink的身影，当然…也包括某些被盗的事件，可以看看它在DeFi中获得的经验，会如何引发它的拓展方向让web3世界变得更加有趣
- [智能合约安全审计入门篇 —— 拒绝服务](https://mp.weixin.qq.com/s/SPc96mQs3VO_OdDLKL1Vow )
  - Joe： 介绍的是智能合约安全中的问题->拒绝服务,又学习到了新知识,对智能合约感兴趣的可以看看

## 982

- [DeFi 借贷领域将如何发展？基于银行业、ETH 质押率等 5 个方面进行探讨](https://zhuanlan.zhihu.com/p/569242031)
  - collin Pan： 通过本文的分析可以帮助你建立一个框架，以便你思考 DeFi 协议以及它们在未来几年在金融服务中可能扮演的角色。
- [wasmtime：Wasm 引擎 1.0 版本发布](https://github.com/bytecodealliance/wasmtime)
  - 亮： wasmtime 经历了 3 年多的开发，1.0 版本终于发布。尽管 1.0 版本才刚刚发布几天，但在此 wasmtime 已经被用到了各个成熟项目之中作为 wasm 执行引擎，提供了一个现代和安全的虚拟机
- [用 markdown 做项目管理](https://markwhen.com/)
  - caos： markwhen 是基于 Markdown 语法的项目管理工具，使用时在左边编辑区里直接分配时间、任务、人员，建立待办清单等等，右边展示区实时渲染显示项目分配和进度 可以导出 pdf 或者 png 文件。

## 980

- [Master Web3 Fundamentals: From Node To Network](https://web3edge.io/fundamentals/master-web3-from-node-to-network/ )
  - Rex： 本文介绍了区块链网络的所有组成部分，适合希望初步了解区块链网络如何运作的读者，几乎每个要点都提供超链接以便深入了解
- [On-Chain Asset Store, Composer and Decentralized Autonomous Marketplace](https://hackmd.io/@snakajima/HJva6n-Jj )
  - Qi Zhou： 怎么样更好的实现 fully on-chain NFT，使得合约和 NFT 的图片数据都可以上链，彻底解决使用第三方数据服务导致可能丢图片的情况，实现 NFT 的所有部分都是用户拥有。
- [Eth2 Book](https://eth2.incessant.ink/book/00__introduction/00__foreword.html )
  - Harry： 一本介绍 Eth2 的电子书。主要内容包括: 共识和区块链的基础知识、Eth1 介绍、Eth2 介绍、PoS 介绍、Eth2 组成、Beacon chain 介绍。

## 978

- [Arbitrum升级后的initialize( )函数漏洞](https://medium.com/@0xriptide/hackers-in-arbitrums-inbox-ca23272641a2)
  - twpony： 当你遇到未初始化的地址变量时，应该停下来看看这个变量是有意不初始化，还是一次重大事故。Arbitrum升级后，重置了前3个slot的变量值，导致initialize()函数可以被再次调用，核心变量bridge可以被赋予任何值。
- [哪种 Optimism 节点更好？公共节点 vs. 私人节点 vs. 自托管节点](https://www.alchemy.com/overviews/optimism-node)
  - AaronChi： 注意本文Alchemy写的：认为在大多数情况下通过 Alchemy 使用私人 Optimism 端点是最好的选择。公共 Optimism 端点不应该服务于产品级别项目的流量，除非你是专业的基础设施工程师，不然你就不应该管理自己的 Optimism 节点。即使你是 Optimism 上临时的交易员或者的 dApp 用户，用一个专门的 Optimism 端点来更新自己的 Metamask 钱包也可以为自己提供给更快且更可靠的服务。
- [Bloxroute 网站](https://bloxroute.com/products/)
  - Survivor： mev mempool研究，Mempool Data号称比其他池快50-200 ms Detect and Protect是核心的研究方向和产品思路，关注mempool的可以看看

## 977

- [写给Bitcoiner的PoS介绍](https://scottmsul.substack.com/p/a-bitcoiners-guide-to-proof-of-stake)
  - CyberOrange：作者认为PoS是负向激励的且有准入的机制，并对Casper和Ghost算法进行了介绍和分析。 中文翻译：https://www.btcstudy.org/2022/09/14/a-bitcoiners-guide-to-proof-of-stake/
- [Ingopedia](https://github.com/ingonyama-zk/ingopedia)
  - 川川：ingonyama整理的关于零知识证明的文章以及资料库。
- [为什么要运行mev-boost？](https://learnblockchain.cn/article/4564)
  - Sam：本文解释了mev-boost如何将以太坊网络中的区块构建者、中继和验证者通过利益关系组建起来，为以太坊提供一个更加去中心化和抗审查的区块生成网络。


## 976

- [慢雾解读 | 区块链分叉带来的安全挑战](https://mp.weixin.qq.com/s/UvotoEeP6_3ZdGZpobY-Xg)
  - Joe： 区块链分叉分为软分叉和硬分叉。本文主要探讨的是硬分叉，一种不支持向后兼容的软件升级方式。同时还介绍了不少分叉网络安全相关的知识点。感兴趣的可以看看
- [Geth State Override 介绍](https://www.libevm.com/2022/01/12/advance-geth-pt-2-stateoverrides/ )
  - OxMax： geth的eth_call方法，可以用来在任何已知的区块链状态上模拟交易执行，State Override提供了一种在模拟前覆盖storage初始状态的能力，极大的扩展了eth_call的作用，但是这个override比较复杂，该文章概括和示例的这种override的原理。
- [Preparing for PRIME: A Look Into the Parallel Universe](https://members.delphidigital.io/reports/preparing-for-prime-a-look-into-the-parallel-universe )
  - Rain： Delphi对Parelle的介绍，这篇文章的主题是探究 Parallel 的 PRIME 代币和 NFT 生态系统的设计基础，以及它们如何相互关联，同时研究参与者、投资者和投机者与经济系统互动的不同策略。

## 975

- [更好用的在线Json文件编辑器](https://jsoncrack.com/editor)
  - Caos： 在线开源的 Json 文件编辑器,可以将复杂 json 结构可视化显示，仓库在此 👇https://github.com/AykutSarac/jsoncrack.com
- [以太坊合并后的混乱市场](https://mp.weixin.qq.com/s/bJxi6cLYhtq0QCYnZ3Oabg)
  - Collin： 统计以太坊合并后一周以来以太坊及分叉项目市场情况，涵盖了价格变动、市场流动性、衍生品、宏观趋势等部分。
- [揭秘以太坊 Vanity 生成器 Profanity 私钥破解漏洞](https://mp.weixin.qq.com/s/jMNWv2YsU9Y1oU1NWXVlMQ)
  - Dream： 这篇文章介绍了主要原理，但是细节没说清楚，其中200万次公钥碰撞，估计作者也没搞清楚为什么是200万， 但是了解原理够了。

## 973

- [基础层中立性 by Paradigm](https://www.paradigm.xyz/2022/09/base-layer-neutrality )
  - Rex： 本文为 Paradigm 对区块链审查的立场文件，其认为区块链 infra 是公共设施，类似TCP/IP 协议，不应该被视为促进非法交易的对象，也不应该被要求协助审查。对基础层的审查将会导致网络出现分叉，破坏其基本价值主张，即区块顺序通用记录。从而使得基础层参与者离岸，提高监管难度，降低可见性，最终损害国家利益。
- [Bringing IBC to Ethereum using ZK-Snarks](https://ethresear.ch/t/bringing-ibc-to-ethereum-using-zk-snarks/13634 )
  - Qi Zhou： 使用 ZKP 来完成成本更低的 Etherum IBC Client
- [Avax 预编译合约的漏洞](https://medium.com/avalancheavax/apricot-phase-6-native-asset-call-deprecation-a7b7a77b850a )
  - Lucas： 继 Pwning 接连发现3个与预编译合约中 caller 相关的漏洞后，avax 也被曝出了与预编译合约相关的漏洞。该漏洞不同于之前的预编译合约漏洞，进一步关注到了预编译合约中发起 call 操作后 callee 的相关安全问题，提供了一个绝妙的漏洞攻击思路以及相关真实案例。


## 972

- [zk Rollup calldata 压缩算法](https://github.com/AntonD3/zk_compression)
  - sny： zk Rollup 的开销大头就是上链 calldata 的压缩. 这个 repo 单独用 zk 电路实现了压缩算法, 可以学习一下这个关键组件的实现。
- [由浅入深理解 ZKP](https://mirror.xyz/cryptoscott.eth/gXwFxebZAi9A9dlZ6Y2yHDEDeP7zzStEXg0th06Wgz0)
  - 昊暠skyh： 一些 zk 概念，数学证明，stark 与 snark，扩容，隐私应用，zkevm 等都在文章里全面讲解。
- [零知识证明 - FPGA vs. GPU](https://mp.weixin.qq.com/s/SjoeQHboe2RI4EJKfpMjKw)
  - shooter： 在寻找更多更好的应用场景的同时，很多人逐步发现零知识证明证明性能是个瓶颈。GPU或者FPGA是目前市面上比较常见的加速平台。本文从MSM的计算入手，分析FPGA和GPU加速零知识证明计算的优缺点。
  

## 971 

- [计算机的自学指南](https://csdiy.wiki)
  - shooter suter： 这是一本计算机的自学指南，这也是一份献给北大信科学弟学妹们的礼物。本书目前包括了以下部分：必学工具、环境配置、经典书籍推荐、国外高质量 CS 课程汇总。
- [Gitcoin Grants 第十五期项目一揽](https://social-layer.notion.site/Gitcoin-GR-15-d10a02daaaef45349b9df411ad5bab25)
  - Aaron Chi： Gitcoin 第 15 轮捐赠活动于 9 月 7 日至 22 日进行，本文为 larri 整理的 9 个 Gitcoin GR 15 宝藏项目及介绍，分别为：去中心化信用体系的基础设施 Ceres、 SBT 基础设施 Social Layer、Web3 经济模型设计框架 BlockModel、Web3 去中心化开源协议 Committable、Web3 社区凭证基础设施 Contri.build、去中心治理 dDAO、Web3 协作社交 join3、华人女性互助社区 Hypatia Galaxy 和 Web3 元宇宙游戏化社交 PartyBoard。
- [Stable Diffusion](https://github.com/CompVis/stable-diffusion)
  - Survivor： 开源版本的AI art，Stable Diffusion 是由 [CompVis](https://link.zhihu.com/?target=https%3A//github.com/CompVis)、[Stability AI (https://link.zhihu.com/?target=https%3A//stability.ai/) 和 [LAION](https://link.zhihu.com/?target=https%3A//laion.ai/) 共同开发的一个文本转图像模型，它通过 [LAION-5B](https://link.zhihu.com/?target=https%3A//laion.ai/blog/laion-5b/) 子集大量的 512x512 图文模型进行训练，我们只要简单的输入一段文本，Stable Diffusion 就可以迅速将其转换为图像

## 970 

- [Algorand State Proofs](ttps://medium.com/algorand/algorand-state-proofs-707d64038e35)
  - 川川：9月7日，Algorand主网进行了升级并增加了状态证明来帮助实现无需信任的跨链通信。本文讲述了Algorand state proofs 的设计理念和应用。
- [零知识证明资源汇总](https://a16zcrypto.com/zero-knowledge-canon/)
  - CyberOrange：A16z发布了一份非常全面的有关零知识证明的资源汇总。
- [从DNS到ENS，域名的Web3时代开启](https://www.8btc.com/article/6777420)
  - River：对Web3域名未来的展望。

## 969

- [以太坊的下一站：上海升级](https://www.odaily.news/p/5181772.html )
  - Mark·R： Ethereum 从pow转pos已经完美达成，让我们看看进击的以太坊下一站：上海 即将完成什么新的计划
- [一文读懂可验证公平性的RNG对Web2生态的价值](https://mp.weixin.qq.com/s/hbHE_dlF0iss11MjMCz4ZA)
  - Joe： 文中介绍了关于Web2领域使用的RNG解决方案所存在的瓶颈，Chainlink VRF如何为Web2应用提供可验证的随机数，以及Chainlink VRF可以实现的一系列用例.感兴趣的可以看看
- [在线 Ethereum RPC Call 组装器](https://etherflow.quiknode.io/)
  - Frozen： 在 Debug 的过程中经常需要临时调查一些合约问题，或者节点问题，使用 etherflow 这个工具可以在线快速组装 RPC call 调用请求，非常方便


## 968

- [dapptools](https://github.com/dapphub/dapptools)
  - Amagi： 一系列智能合约 cli 工具，部署，测试，签名，查询 应有尽有
- [咖啡馆模拟器](https://imissmycafe.com/)
  - Caos： 咖啡馆模拟器, 一个模拟咖啡馆环境音效的网站可以连接 Spotify, 疫情期间被封控在家, 靠它沉浸式喝了几天️
- [ZK Hack 零知识证明教程 —— 模块二](https://mp.weixin.qq.com/s/sga6rhc_jtOQfOEAvgqOEQ)
  - 张晓： 应该是全网质量最高的零知识证明入门教程了，这里是zCloak团队翻译的第二部。

## 966

- [Delphi labs 的「新」选择](https://members.delphidigital.io/reports/finding-a-home-for-labs)
  - harry： 继 Luna 崩盘后，Delphi labs 一直在寻找自己的方向，最后分析了各个生态一圈下来，他们觉得还是 cosmos 最适合自己。他们用研究来帮助自己做选择这一点还是值得学习的
- [Can Machines Learn without Knowledge?](https://cronokirby.substack.com/p/can-machines-learn-without-knowledge)
  - msfew： 机器学习与 zk 的融合可能性
- [以太坊协议层的七年之变](https://mirror.xyz/0xbeC73ba0817403cd11C11bE891D671EA30443562/aAjZCMWwd_GEfqiLJNVzqvpOTuk0202Iotyx-KQ-oHI)
  - TimeOfSand： Eth 自 2015-07-30 上线起，共进行了 14 次硬分叉，包含 39 个 EIP（「君士坦丁堡」和「彼得堡」视为同一次）。间隔最近的两次硬分叉是 26 天，间隔最远的两次则是 490 天。本文将回顾以太坊的历次硬分叉及其中包含的改进提案，试图展现过去的七年中以太坊究竟做了什么。


## 965 

- [渐进式所有权优化（Goo）机制完整运作模式](https://mp.weixin.qq.com/s/XmikIg7hm5mN4I6-cOAgtg)
  - Feng Liu： 简单来说，ERC20 代币发行增速很快，不持有 NFT 或者随便卖掉 ERC20 代币，自己手头的份额就会被稀释。利用这种机制，不持有 NFT 而只持有对应的 ERC20 代币非常不合算，因为其他拥有 NFT 的人会获得更多对应的 ERC20 代币，你在供应量中的份额会迅速减少。持有 NFT，但是随便卖币，也会遭遇份额被稀释的问题，就是要利用高通胀实现强制锁仓
- [ETH2.0 merge 后 MEV 预期收入分析](https://pintail.xyz/posts/post-merge-mev)
  - 李一笑： 合并后出块时间将会固定为 12s，不过出块时间的减少并不会显著的影响 MEV。validator 预计可获得 apr 6.1% 的收益（最低 5.3% 最高 7.3%）
- [一键搬迁eth钱包资产](https://sweeposaurus.com)
  - frozen： 作为 Web3 用户经常需要更换地址，但是地址上拥有大量不同类型资产时迁移成本极高。这是一个网页小工具，可以一键将钱包所有资产转移到新的钱包地址。

## 964

- [Nginx 反向代理的 Web 管理界面](https://github.com/NginxProxyManager/nginx-proxy-manager)
  - Shooter：这个项目作为一个预构建的docker镜像，使你能够轻松地转发网站，包括免费的SSL，而不需要对Nginx或Letsencrypt了解太多。
- [以太坊合并前，要不要借入 ETH？](https://foresightnews.pro/article/detail/13645)
  - Sam：Euler Finance 创始人帮我们系统预测了以太坊合并前借入以太坊可能需要承担的利率和市场博弈情况。
- [AI + NFT：什么是 iNFT？](https://mp.weixin.qq.com/s/jhD5tl5T0AIMNGzvAmp0bQ)
  - River：iNFT简单介绍。 


## 963 

- [Gitcoin Grant 的 6 个数据故事](https://blog.mest.io/zh-cn/posts/gitcoin-story)
  - shooter： Gitcoin Grant 过去 14 轮总共募资 18.04M，其中 66.93% 来自社区用户捐赠，33.07% 来自匹配池。期间总共有 142,776 用户参与捐赠，贡献 2,380,689 笔捐赠，让 3,161 个项目受益。
- [暗夜小偷：Redline Stealer 木马盗币分析](https://mp.weixin.qq.com/s/MibasyXTwnQsLI_nd_XwBQ)
  - Joe： Web3 兴起，加密货币越来越流行,很多攻击开始针对钱包盗取秘钥,保护好我们的钱包变得越来越重要的,每天一个小知识,让我们保护好自己的数字资产
- [Euler Finance创始人：以太坊合并前，要不要借入ETH？](https://www.odaily.news/post/5181619)
  - Mark·R： 即将到来的合并和分叉，从收益视角分析持有eth的方式如何最优

## 962

- [漫画图解比特币](https://whitepaper.coinspice.io/cn)
  - Caos： 传奇漫画大师—— Scott McCloud, 被称为是漫画界的亚里士多德, 他绘制的《图解比特币白皮书》是中本聪原论文的漫画版，有趣易懂，建议大家都看一看！
- [CodeEdit 原生Mac code editor](https://github.com/CodeEditApp/CodeEdit)
  - 陈锡亮： CodeEdit是一个新的code editor，用Swift开发，目的是打造原生mac应用，不必依赖Electron，带来MacOS最优的体验
- [AMM 进化史](https://mp.weixin.qq.com/s/3FKiOnXheP4CT72ffgaT2g)
  - Collin： 是一篇AMM进化史想了解AMM值得一读，文章详细介绍了自动做市的历史以及它是如何从简单的代数方程演变为创新的生态系统。

## 961

- [在线解析 Ethereum Tx 数据](https://www.moesif.com/solidity-abi-hex-decoder/decode)
  - Frozen： 经常随手需要看 Ethereum 上的 Tx 数据，通过在线工具可以方便的解析对应数据，方便查看，节省时间.
- [Foresight Ventures: 从 Polygon zkEVM 出发, 理解 zkEVM Rollup](https://mp.weixin.qq.com/s/i9O0vpHnkHFwVBPjNeqMUQ)
  - 张晓-zCloak： Foresight 对 zkEVM 的解读文章。
- [应用 VS 基础设施，WEB3 的下一个 Alpha 在哪儿？](https://techflowpost.substack.com/p/vs-web3-alpha )
  - Qi Zhou： 新周期中的重点关注在哪里？本文多角度阐述了 Web3 基础设施与应用的关系。

## 959

- [合并之后 TWAP 的变化](https://drive.google.com/file/d/1fuuKGWdaVdXQoN_d4Riq4rsSB5OtzaJA/view)
  - sny： Uniswap V2 V3 的 TWAP 一直是链上价格预言机的标准. 但是 Merge 之后对它的攻击成本等方面都有影响, 作者提出了一些改进方案。
- [KK 的 103 条智慧箴言](https://kk.org/thetechnium/103-bits-of-advice-i-wish-i-had-known/)
  - Harry： KK 70 岁了，他总结了 103 条建议给年轻人。我摘抄前 3 条过来：1. About 99% of the time, the right time is right now. 2. No one is as impressed with your possessions as you are. 3. Dont ever work for someone you dont want to become. P.S. 第三条是激发我转行到科技行业的原因之一。
- [The Evolution of AMMs](https://bennyattar.substack.com/p/the-evolution-of-amms?utm_source=twitter&sd=pf)
  - Rain： 作者简要介绍了自动做市商的历史以及它如何在加密领域的演变。然后，讨论了 AMM 的三代及其在加密市场中的细分市场。

## 958

- [tornado协议详解](https://www.coincenter.org/education/advanced-topics/how-does-tornado-cash-work/)
  - twpony： 本文深入剖析了tornado协议架构、设计理念、运转方式，并附上了核心合约的链上地址，方便进一步分析源码和交易。
- [Vitalik: zk-SNARK 何以可能 —— 引介杀手锏“多项式承诺”](https://mp.weixin.qq.com/s/zObA93LTxJiWbAuB987cNQ)
  - YAN： V神的zk 科普。
- [Ethereum Protocol Fellowship: The Third Cohort](https://blog.ethereum.org/2022/09/01/ethereum-protocol-fellowship-third)
  - 昊暠skyh： 以太坊基金会宣布新一期以太坊协议奖学金（Ethereum Protocol Fellowship）开始接受申请，9 月 16 日截止，该计划将正式运行 4 个月（2022 年 10 月-2023 年 1 月），选定的参与者将获得来自以太坊核心开发社区的导师，还有机会参与对以太坊有实际影响的项目。根据介绍，以太坊协议奖学金是一个旨在让开发人员参与协议开发过程的计划，候选人需要很强的口头和书面沟通能力，拥有技术基础，能够编写代码。


## 957

- [Game Smart Contracts](https://github.com/curio-research/contracts)
  - Amagi：一个使用EIP-2535作为标准，Curio的游戏智能合约代码。
- [Interview: Vitalik Buterin, creator of Ethereum](https://noahpinion.substack.com/p/interview-vitalik-buterin-creator)
  - 川川：在本篇采访中,Vitalik重申了对于迈进POS的坚定, 他认为其中的驱动力之一是POS有较低的持续成本和较高的准入成本，极大的维护了维护了网络的安全性，并且即使在网络被受到攻击的情况下，相较PoW更改算法的方式，POS也可以保证生态有较短的恢复周期。另外他强调大家应该更多的去把治理机制和共识机制分开，治理应交由多方利益的代表人，而非是形成一个简单的“代币驱动治理”的机制，从而避免粗暴的认为权益证明可以让协议被金钱收买。
- [看项目生态和开发者的网站](https://www.gokustats.xyz/developers)
  - 昊暠skyh：项目抢占开发者高地，一定程度反映价格。

## 956

- [DuckDuckGo开放邮件保护服务](https://duckduckgo.com/email/)
  - Caos： DuckDuckGo 推出了自己的匿名邮件转发服务，有兴趣的可以注册一个@duck.com 结尾的邮箱可以将邮件转发至你真实的邮箱里,同时也 DuckDuckGo 也可以帮助你生成一个一次性的邮箱地址,方便实用
- [lunatic：另一个基于 actor 模型的 wasm 运行时？](https://github.com/lunatic-solutions/lunatic)
  - Shooter： lunatic 是一个借鉴了 Erlang VM 众多理念的 wasm 运行时，不过目前主要支持 Rust。  Rust 写的程序编译成 wasm 后，用 lunatic 虚拟机运行可以拥有 Erlang 那种高容错，高并发的体验。 波卡生态的 Gear 与 Cosmos 的 CosmWasm 都在开发基于 actor 模型的 wasm 运行环境。
- [DeFi 链上基金赛道格局与趋势](https://foresightnews.pro/article/detail/12823)
  - 昊暠skyh： DeFi 链上基金的三种分类：算法策略基金、主动策略基金，以及被动策略基金。风险：主动策略基金>算法策略基金 >被动策略基金。收益：主动策略基金>算法策略基金 >被动策略基金。

## 954 

- [Aptos vs. Sui：详细对比](https://mp.weixin.qq.com/s/tj3m4oa3mk-3HWjYBg_Ofg)
  - 张晓-zCloak： Meta 系新公链的详细对比，给大家参考。
- [以太坊开发者会议总结：关于协议级抗审查的应对策略](https://news.marsbit.co/20220821092732290435.html )
  - Qi Zhou： 以太坊抗审查的最近会议总结，创始人 Micah Zoltu 提出了两个与网络审查有关的具体问题：MEV-Boost Relays 审查和 Staking 服务商的审查
- [Dextools](https://www.dextools.io/ )
  - Lucas： Dextools 可以帮助交易员做出更好的交易决策。有了这样一个工具，交易者可以获得相对的信息优势和时间优势，从而有机会实施更好的交易或投资策略。同时对于 Rugpull 以及攻击事件分析人员也提供了便捷的分析手段。

## 953

- [一个探索去中心化应用的网站](https://www.stateofthedapps.com/)
  - Zhangxuesong： State of the DApps 最初是为了分类和展示基于以太坊开发的项目，最近我们增加了对 EOS、Klaytn，Steem 和 Hive 等的支持。包含所有dapp统计数据且每天更新。经常看看 DApp 生态系统的进展和发展。
- [Specular Network](https://specular.network)
  - sny： 通过 EVM-native 的 ORU 解决了现有 Optimistic Rollup 的一些问题
- [如何建立一个国家](https://thenetworkstate.com/the-network-state-in-one-thousand-words)
  - Harry： 在 《The Network State》这本书中，作者阐述了建立一个网络国家分为 7 步：1. 建立一个线上的小型社会，可以理解为是一个在线社区 2. 组织人们集体行动 3. 构建线下信任和在线加密经济 4. 众筹线下节点，可以一起居住 5. 将线下社区通过数字化技术连接起来 6. 进行在线人口普查，证明社会在壮大 7. 获得外交认可，最终成为真正的网络国家
  

## 952 

- [链上交易的分析方法和分析工具](https://twitter.com/Merkle3_/status/1559864278512959488?utm_source=substack&utm_medium=email)
  - twpony： 通过Nomad ERC20 Bridge被盗实例，展示了被盗过程的分析方法和分析工具。
- [慢雾：Web3 安全事件一周回顾](https://twitter.com/SlowMist_Team/status/1563536054543872004)
  - Aaron Chi： 1）Sudoswap 仿盘 Sudorare 发生 Rug Pull，攻击者获利约 519 枚 ETH；2） PokémonFi 主题 NFT 项目 PokemonFi 发生 Rug Pull，用户损失超 70 万美金；3）攻击者通过 REVOKE 诱骗用户签署恶意合约；4）服务商 Twilio 受供应链攻击影响后产生多米诺效应，导致 DoorDash、Okta 等也受到影响。
- [开源的密钥管理系统](https://github.com/hashicorp/vault)
  - Survivor： 特征：安全私密存储、动态密码、数据加密、租赁和续订、密钥撤销等特征，已经持续维护7年之久的稳定系统，管理各种key，password等等。

## 951 

- [noxx大佬的博客](https://noxx.substack.com/)
  - 昊暠skyh：硬核的博客，一篇可以看一周，包含evm，mev，arbitrage等。
- [Monaco Game](https://0xmonaco.ctf.paradigm.xyz/)
  - Zhangxuesong：0xMonaco 是一个链上的 pvp 赛车竞速游戏，设计的目标是测试你的游戏理论 和 pvp 排位。
- [Data Availability Sampling: From Basics to Open Problems](https://www.paradigm.xyz/2022/08/das)
  - CyberOrange：Paradigm也来给大家科普数据可用性采样了。
  
## 950

- [A design system for building retro Apple interfaces](https://github.com/sakofchit/system.css)
  - Caos： 一个用于构建复古苹果界面的开源 CSS 仓库,喜欢复古风格设计的开发者可以尝试一下
- [从第一原理理解 rollup 经济学](https://www.ethereum.cn/Layer2/understanding-rollup-economics)
  - Collin： 本文将首先确定 rollup 系统中的各部分，它们的角色和职责。接着梳理出 rollup 系统中的各种开销、收益与费用，从而给我们提供一些线索如何探索这个广泛的设计空间。
- [2022 年去中心化稳定币现状：25+ 项目分析](https://medium.com/@Ignas_defi_research/state-of-decentralized-stablecoins-in-2022-analysis-of-25-projects-8fbdd458ecdd)
  - Harry： 稳定币占 1.07 万亿美元加密市场总额的 14.2%。本文研究了 25 个去中心化稳定币项目，试图分析这些项目的运作模式与锚定机制、用例与风险以及如何扩大发行规模。


## 948

- [当前 Rollup 生态解读](https://www.alexbeckett.xyz/a-snapshot-of-the-current-rollup-ecosystem/)
  - Frozen： 文章解读了 Rollup 生态发展现状，介绍了 Optimistic Rollup、zk Rollup 等等不同类型的 Rollup
- [Aptos 中交易并行处理的STM方法](https://medium.com/aptoslabs/block-stm-how-we-execute-over-160k-transactions-per-second-on-the-aptos-blockchain-3b003657e4ba )
  - Lucas： 该文章简要的解释了 Aptos 中的 Block-STM 技术，有助于进一步理解 Aptos 中交易并行执行的设计方案。
- [链上数据归集查询工具](https://www.watchers.pro/ )
  - 松： 推荐一个链上数据归集查询工具。支持以太坊地址、ENS、代币或项目名称的查询。

## 947 

- [BendDAO 中文版使用说明](https://github.com/cgq0123/bend-gitbook-portal/blob/chinese-v2/SUMMARY.md)
  - shooter： BendDAO 是一个 NFT 流动性协议，支持即时 NFT 支持的贷款，抵押品挂单和 NFT 首付。首付、借款和挂单的无缝体验为用户创造了一个完美的闭环，是一站式 NFT 流动性解决方案。
- [小白如何进入 Web3](https://twitter.com/charlie_cao/status/1560955261250314241)
  - Harry： 一个 twitter thread，从通识教育的角度，对小白如何进入 Web3 做一些简单的梳理
- [一些不去中心化的 DApp](https://github.com/Nemusonaneko/projects-with-restrictions)
  - sny： 这个仓库里包含了以各种方式为了合规而内置 censorship 的 DApp, 包括 Aave, Pokt, Flashbots, Uniswap, dYdX.


## 946

- [开源+云服务的password、passcode、passkeys三种验证模式集成](https://www.hanko.io/)
  - Survivor： 开源开放，支持PSD2 SCA and SSO，支持手机生物识别（指纹、人脸等）、设备指纹、USB FIDO设备keys、Email、Mobile SMS等认证方式，开源且被稳定应用于生产环境。
- [深度解读Optimism：基本架构、Gas机制与挑战 | CatcherVC Research](https://mirror.xyz/0x8C4d5E90196325FB22Fff37C97D7984a37e51D11/5Y8BUo8PvJe2xGKExbxmPBaYaInuaYZlFt8_KlW9UpA)
  - NaNa： 在确保去中心化的前提下，Layer1扩容所面临的困境，而以Optimism和Arbitrum为代表的Layer2凭借高效率、低Gas的旗号快速崛起，颇为瞩目。但典型的高效Layer2项目依旧存在着重大问题。本文将从底层架构深度解读Optimism的机遇与挑战。
- [慢雾：Solana 公链大规模盗币事件的分析（续)](https://mp.weixin.qq.com/s/YJgPNYykr2P1j2yHseJ2tQ)
  - NaNa： Solana 公链大规模盗币事件回顾及分析；


## 944 

- [function musicForProgramming](https://musicforprogramming.net)
  - Caos： 这是一个专门给开发者工作的时候听歌的电台，歌单是作者经过多年的反复试验挑选出来的，都是一些能够让人专注的音乐。界面非常干净，操作也很硬核。确实听音乐会让你快乐。当你快乐时，你会更有效地工作
- [Tornado Cash发生了什么](https://www.theblockbeats.info/news/31545)
  - Collin： 关于美国财政部制裁 Tornado Cash 的解读
- [Web 3.0畅想：去中心化之后呢？——多中心化](https://mp.weixin.qq.com/s/XnfLATE2yNad_xfR7esELA)
  - Amagi： 入门web3.0靠的是修心？门槛在于对心智模式转变是否保持开放性？
  

## 943


- [解释亚马逊收购irobot的真相：更深入的监控你的生活](https://medium.com/swlh/the-real-reason-amazon-just-bought-irobot-is-to-spy-deep-inside-your-home-a7e3538cde4d)
  - 北美开吻： 当你家里有各种Smart Home的IoT产品，以为你自己可以使用高科技做自己生活的主人的时候可曾想过，还有一批科技大佬来当你的主人呢。不知道川普家里是不是也有Roomba来扫地，结果被FBI发现这里有绝密文件，于是产生了后来的踹总统门事件。
- [作为隐私计算的重要解决方案的Intel SGX又又又又又被发现漏洞了](https://arstechnica.com/information-technology/2022/08/architectural-bug-in-some-intel-cpus-is-more-bad-news-for-sgx-users/)
  - 北美开吻： 难怪他们自己也都进行了战略中心的转移。这东西的确很难。
- [Cryptocurrency Icons](https://github.com/spothq/cryptocurrency-icons)
  - Aaron Chi： crypto icon合集，完全免费，png + svg

## 942

- [如何 hack 我的现代 Ioniq SEL 汽车？](https://programmingwithstyle.com/posts/howihackedmycar/)
  - shooter： 一位博主发现用于签署现代汽车软件更新的私钥，来自一篇关于 OpenSSL 的公开教程，让我们看接下来的故事 ......
- [OP in Paris: Kelvin Fichter](https://www.youtube.com/watch?app=desktop&v=wTsmYKfTVIg&feature=youtu.be)
  - sny： Kelvin Fichter 讲述 Optimism 的未来路线. 包括 Bedrock 和 Proof 的发展.
- [论账户抽象(2022)](https://mirror.xyz/0xbeC73ba0817403cd11C11bE891D671EA30443562/95LlE7sLCL4UTvL7rU3ZAXnBvlDbh7X-rm0QWkc43Us)
  - Harry： 前半部分讲了账户抽象是什么，外部地址（没有账户抽象）的优势。最后抛开 ERC-4337 的利弊，提出了两个问题。1. 是否所有功能都要通过系统内共识完成，即使它带来极大的缺点和不确定性？2. 建立在特定 MEV 解决方案基础上的账户抽象是否稳健，假设 Flashbots 的方案最终被淘汰，用户需要直接和矿工/验证者打交道？

## 941

- [借助示波器、抓包工具分析UDP数据包](https://www.mattkeeter.com/blog/2022-08-11-udp/)
  - twpony： 通过示波器采集UDP协议传输时产生的电压波形图，并把电压波形图还原成数据包，抓包工具判断还原的数据包是否与真实数据包吻合。
- [尝试用 zellij 替代 tmux](https://github.com/zellij-org/zellij)
  - shooter suter： Zellij 是一款非常优秀的终端工作区和多路复用器（类似 tmux、screen），由 Rust 语言开发。它最重要的功能包括布局系统可编译为 WASM 插件。
- [NFTs - Licensed vs CC0](https://twitter.com/NFTSabeen/status/1555262778297831427)
  - 昊暠skyh： 例举了一些许可案例和无许可案例现在的nft标准，数字+艺术的知识产权了解一下。

## 939

- [Move Book中文版](https://github.com/move-dao/move-book-zh)
  - 雪松： Move语言教程中文翻译版本，适合一些英文不是很好的开发者。
- [新公链生态盘点：Aptos、Sui、Linera、Aleo](https://www.notion.so/bitalknews/Aptos-Sui-Fuel-Milkomeda-2370fa4cf7324757860f3bd170dfd649)
  - Gala： 本文盘点了 Aptos、Sui、Linera、Aleo、Celestria、Fuel、Scroll、Espresso、Milkomeda 共九条条新公链生态进展及项目部署。
- [Aptos 官方解读白皮书：Move 语言建构下的高安全性公链](https://www.theblockbeats.info/news/31570)
  - Gala： Aptos 公布了 V1.0 版白皮书「安全、可拓展和升级的 Web3 基础设施」，本文为 Aptos 官方对其愿景、安全、性能、智能合约并行执行引擎、状态同步及可升级性的介绍。

## 938

- [量化交易全面开源计划预告1 - b站](https://www.bilibili.com/video/BV18g411C7rQ)
  - Amagi： 系统性地从 高频套利 / AI自动策略 / 全球交易 / open trading 接触与学习金融量化
- [A Hex Editor for Reverse Engineers](https://github.com/WerWolv/ImHex)
  - 陈锡亮： ImHex 提供了强大的二进制数据查询，搜索，反编译功能，可以用来反编译文件内存，二进制文件等等
- [详解以太坊2.0与硬分叉](https://www.bitpush.news/articles/2994917)
  - Collin： 详解以太坊2.0将会有哪些重要升级，POS权益证明机制下的以太坊惩罚机制以及面临硬分叉会发生什么

## 936

- [ZK Hack 零知识证明教程 —— 模块](https://mp.weixin.qq.com/s/7nhdAOxorjbTIUfGiwhzrA)
  - 张晓： 斯坦福大学的Dan Boneh教授讲解，可以说是目前能找到的最优秀的零知识证明入门教程了。zCloak团队将翻译全系列教程
- [Safari 15 IndexedDB 漏洞](https://fingerprint.com/blog/indexeddb-api-browser-vulnerability-safari-15/)
  - Barret： 如果你在前端使用了 IndexedDB 进行用户资源/数据的储存，需要关注下是否包含敏感数据，如联系方式或登录信息等。苹果在这个版本的 IndexedDB 上漏掉了同源限制，漏洞可跨站随意获取 IndexedDB 中的数据。
- [RUST语言的编程范式](https://coolshell.cn/articles/20845.html)
  - 耗子叔： 本文主要想通过Rust的语言设计来看看编程中的一些挑战。Java 与 Rust 在改善C/C++上走了完全不同的两条路，他们主要改善的问题就是C/C++ Safety的问题。Rust不玩垃圾回收，也不玩VM，所以，作为静态语言的它，只能在编译器上下工夫。

## 934

- [DeGen测试机](https://degenscore.com/profile/jhfnetboy)
  - Survivor： 一个测试你的DeGen分数的网站，很好玩，隐私保护，不会泄露你地址，猜测是根据多链的交易记录和资产持有等等加权计算的，算法未公开，我竟然只有100多分。
- [secureum安全规范](https://secureum.substack.com/)
  - twpony： secureum总结了以太坊在安全漏洞、最佳实践、审计发现、solidity方面各100条建议，简短精悍，能够全方面了解以太坊安全规范和最佳实践。
- [以太坊区块链账户抽象概览](https://www.ethereum.cn/Technology/account-abstraction)
  - 昊暠skyh： EIP-4337：通过入口点合约实现的账户抽象。这是 Vitalik Buterin 和社区提出的最新议案。它作为一项 ERC 提议出来，而这个提案包括了避免共识层协议的变更，而依靠于更高层的基础设施。关键亮点：没有中心化的活动者、移除了用户端钱包设置复杂性，完全支持 EIP-1559、具有代替交费的能力，发送一个比旧 UserOperation 具有更高溢价的新 UserOperation 来替换操作或保留了让它更快被打包的功能。一些新优势：验证逻辑的灵活性；足以让执行层达到量子安全；钱包可升级性；执行逻辑灵活性。

## 932

- [fffuel - SVG图形生成器](https://fffuel.co/)
  - Caos： 一个非常好看的 SVG 背景和图形的生成器，可以自定义很多参数，自由度很高。很多图形直接拿来做壁纸都很好看，拿来做文章标题海报背景，很不错。
- [通过 Wormhole 桥开发跨链应用 xDapp](https://book.wormholenetwork.com/introduction/introduction.html)
  - 陈锡亮： 本文介绍了什么是跨链应用 xDapp，以及如何通过 Wormhole 跨链桥的消息传输协议来开发跨链应用
- [MEV 的未来](https://www.chaincatcher.com/article/2077438)
  - Collin： 探讨 MEV 在不同加密经济系统中的最终状态

## 931

- [How safe are cross-channel bridges?](https://medium.com/web3-magazine/how-safe-are-cross-channel-bridges-554a2615505)
  - 北美开吻： 本文列举了最近几次跨链桥攻击原理. 结论就是没有结论. 跨链桥有太大的攻击面而且很难做到安全. 相对于 Defi 类型的合约, 跨链桥需要更多的检查监督代码,验证 key 和安全通讯. 这些增加的复杂度就造成了更多的 bug 和隐含的攻击机会.
- [去中心化彩票协议](https://azuro.org)
  - Mark Tang： Azuro 是一个去中心化博彩基础层，最近在看一款叫 bookmaker.XYZ 的项目了解到了 Azuro。Azuro 将线上博彩分成了 Betters、Liquidity Providers、Data & feed Providers、Azuro DAO 几个部分构建了 Azuro protocol，无论你是博彩生态中的哪个角色都能很好参与进去，对彩票感兴趣的小伙伴可以研究一下
- [zCloak Network：从浏览器插件杀入隐私计算赛道的新势力｜链茶访](https://mp.weixin.qq.com/s/nGkQXSIpzm1fwXaNsGdGEQ)
  - 张晓-冰链科技： 本文介绍了 zCloak Network ，链茶馆的专访非常详细。

## 930 

- [Nim：新的静态语言](https://nim-lang.org )
  - Frozen： Nim 作为新的静态语言，汲取了过往的编程语言特性，高效且优雅。目前 openSUSE 已经在发行版中添加 Nim 支持
- [Web3 安全的帽子戏法](https://pwning.mirror.xyz/ )
  - Lucas： 著名黑客 PWNING 在上述博客中公开了其挖到的3个严重漏洞，总赏金高达 700w 美元，对于学习和积累区块链安全知识有极高的价值。
- [Has Curve V2 lost the great DEX battle to Uniswap V3?](https://twitter.com/JiBofan/status/1551714529167323136)
  - Qizhou： Curve V2 和 Uniswap V3 在 DEX 大战的表现

## 929

- [描绘, 分类, 支配 MEV](https://mp.weixin.qq.com/s/Yd-umFnjhXyB7crei2lynA)
  - sny： 一篇对 MEV 的原理、分类与不同场景下应对策略的比较详细的分析文章。
- [Web3 社区增长指南](https://wakeful-bubbler-534.notion.site/7b13f83b36c246edbb99a0c57c0a7ab7?v=6be5330cb0c7447fb74184242f090d74)
  - Harry： Safary club 整理的一系列文章，给关注 web3 社区增长的从业者。
- [产品沉思录](https://pmthinking.notion.site/pmthinking/ProductThinking-a601a12335044f349a22caf57f274c27)
  - Survivor： 这里面有关于交易平台、社区设计、学习思维等等，是一个很不错的学习花园。

## 928

- [Awesome Move](https://github.com/MystenLabs/awesome-move)
  - Zhangxuesong： 最近move的成为了讨论的热点，这里罗列了涉及 move 编程语言社区的一些优秀代码和一些典型项目。
- [宏观分析的全面数据网站](https://www.koyfin.com/)
  - urvivor： 宏观分析的全面数据网站，基金，货币，商品，指数，固收，从石油到黄金，历史数据对比，趋势提取，各种图表，关键是免费版就足够你常规分析用了，不错的网站
- [Blueboat - all-in-one, multi-tenant serverless JavaScript runtime](https://blueboat.io)
  - Aaron Chi： 类似于Cloudflare Workers的开源实现，Blueboat使用wasm做到冷启动17ms，编写的体验非常强调“分布式”概念


## 927

- [The network state in one essay](https://thenetworkstate.com/the-network-state-in-one-essay)
  - Koi：用一篇文章简要概括了，可以作为开始这本书之前的导读。
- [一文看懂数据可用性（DA）对区块链的重要意义](https://www.8btc.com/article/6721900)
  - Sam：此文可以帮助我们理解，为什么数据可用性对rollup发挥全部潜力是至关重要的。
- [P2P点对点NFT交易平台sudoswap](https://nftboard.today/sudoswap-introduction/)
  - River：这是一篇关于sudoswap的教学介绍。

## 926

- [深入研究区块链的模块化组件](https://volt.capital/blog/modular-blockchains)
  - Amagi： 作者对现有的区块链会碰到的技术难题与可能性提出了探讨，将可结合的技术组件拆分并给出了一个可行的架构方案。
- [可抄作业的产品文案库](https://copybook.me/)
  - Caos： 这是一个产品中常用文案的集合，开发网站或者是 APP 的时候有些文案不会写可以在这里抄作业。
- [HTTP 流量拷贝测试神器 GoReplay](https://mp.weixin.qq.com/s/hgmVBerO8K2lA0iqXzipMg)
  - Collin： GoReplay是一个用Golang编写的开源的流量复制工具, 是一种非侵入软件方式的流量复制方式。GoReplay将监听到的流量，以HTTP发送到指定的一个或者多台测试机器，测试人员可以在测试机器上对流量处理结果进行分析。

## 923

- [闪电网络简史](https://mp.weixin.qq.com/s/kzXue8yx8wHQqr3mtQW4GQ)
  - 昊暠skyh： 本文讲了闪电网络的一些思考和历史方案
- [Celestia Node Types](https://docs.google.com/presentation/d/1cPkRaaE15XV99o6H5SOzxPqbIrBr35-HCT29mMctvRA/mobilepresent?slide=id.gc6fa3c898_0_0)
  - sny： Celestia 网络中各种不同节点的介绍, 其中节点分为共识层和 DA 层两大类。
- [底层公链 Ergo 和 Nervos CKB 的异同点](https://talk.nervos.org/t/ergo-nervos-ckb/6668)
  - Harry： Jan 聊了下 Ergo 和 Nervos CKB 这两个 UTXO 公链的对比。
  

## 922

- [各种奇怪的ERC20](https://github.com/d-xo/weird-erc20)
  - twpony： 我们日常接触的ERC20，大多是遵照ERC20规范部署的token。但是ERC20的标准非常宽松，有很多不同寻常的ERC20被部署在链上，这些ERC20的行为和我们日常熟知的规范大相径庭，怪异的行为也可能会引起安全事故。本项目总结了各种链上真实存在的ERC20以及与之相关的安全注意事项。
- [008 JX | 蓬勃和混乱并存，这届NFT NYC的沉浸式体验](https://www.xiaoyuzhoufm.com/episode/62c0d8c795d586519ac8894a)
  - Aaron Chi： 史上参会人数最多的NFT NYC上周落幕了。Web3 Revolution播客邀请来了一位NFT收藏家JX来讲一讲沉浸式的NFT NYC体验，从主会场的混乱到蓝筹NFT parties的惊喜和蓬勃，蹦迪、party、社交、还是NFT roadmap的呈现并存。NFT真实的线下风气是怎么样？
- [EVM puzzles](https://github.com/fvictorio/evm-puzzles)
  - Zhangxuesong： 一个能让你熟悉evm操作码的闯关小游戏。一共10道题目。

## 921

- [运行一个非等级化的组织](https://commonknowledge.coop/writing/non-hierarchical-organising/)
  - CyberOrange：本文讲述了作者对于非等级化组织的理解和协作方式
- [The Ownership Economy and the Future of Media](https://www.8btc.com/article/6766642)
  - River：所有权经济将定义下一个媒体时代，NFT、DAO等是转型过渡的助推力量。
- [face2comics](https://github.com/Sxela/face2comics)
  - River：外国一位名为 Alex 的开发者整理的人脸转漫画数据集。

## 920

- [次时代基于deno的web开发框架](https://github.com/denoland/fresh)
  - 陈锡亮： fresh全程使用边缘计算做渲染，没有前端JS渲染，速度爆表，TS开发者可以一试
- [人体系统调优不完全指南](https://github.com/zijie0/HumanSystemOptimization)
  - Caos： 健康学习到150岁 - 人体系统调优不完全指南
- [以太坊合并后，你应该明白这 10 件事](https://zhuanlan.zhihu.com/p/541417100)
  - Collin： 以太坊合并后的 PoS 时代的 10 个重要特征


## 918

- [Web3启示录：DAOs网络如何实现DAO的去中心化治理（理论与方案)](https://mp.weixin.qq.com/s/Z1bHfaEAVuVMLcKXYhty6Q)
  - Survivor： 关于 DAO 的长文，基本快达到 Paper 的级别了，论述了 DAO 网络方式实现去中心化治理的思考
- [以太坊基金会研究团队第八次 AMA 之扩容专题](https://www.ethereum.cn/Layer2/ama_we_are_the_efs_research_team_8_p1)
  - Qizhou： 什么是 enshrined rollup?本次 AMA 中开发者们回答了许多关于扩容的研发进展以及遇到的难题
- [Ethermint链任意代币增发漏洞详解](https://www.iczc.me/post/ethermint-cve-2021-25837/ )
  - Lucas： 作者具体讲解了其所挖到的 ethermint 增发代币漏洞，该漏洞核心问题在于 cache 状态在交易失败回滚时没有及时进行相应处理导致。作者从 cosmos 生态细节从浅入深的讲解了该漏洞的全貌，在认识漏洞本身原理同时，也可以增加对 cosmos sdk 的了解认识

## 917

- [能让你熟悉evm操作码的闯关小游戏](https://github.com/fvictorio/evm-puzzles)
  - Zhangxuesong： 一个能让你熟悉evm操作码的闯关小游戏。一共10道题目.
- [GitHub贡献徽章NFT](https://github.com/Jon-Becker/renoun)
  - sny： 链上生成与渲染的GitHub贡献徽章NFT，无法transfer，可以作为开发者身份的展示。
- [Collected web3 twitter threads](https://cdixon.mirror.xyz/TNOgrQGh_xUnBVO7wuYB-NMajrc3_0zN20-XznJRKlk)
  - Harr： Chris Dixon 发表的关于 web3 的 twitter threads。

## 916

- [Let's Build a Simple Database](https://github.com/cstack/db_tutorial)
  - River：对于想了解与认识数据库基本原理的同学来说，这本书不可错过。
- [重新定义 NFT (音乐、四维、隐私、交互)](https://mp.weixin.qq.com/s/c-sP6lbpVjFyAOreR35UOg)
  - River：本文讲述了作者对于未来NFT的畅想
- [Gavin Wood：波卡治理 v2 会有哪些变化？](https://mp.weixin.qq.com/s/tejMz3YrqhZ0W0uT-2rWc)
  - Joe：文章记录了波卡创始人 Gavin Wood 关于波卡治理 v2 发表的演讲，简要叙述了新版治理体系的内容，和其与原体系的差别,内容比较详细,特别是对治理这块做了很多总结和分享,感兴趣的可以看看(分两篇文章记录,第二篇链接:https://mp.weixin.qq.com/s/ZjdrsgSBJoyaS6Lgfof7gg)

## 915

- [PartyDAO的故事 – 一群陌生人如何在1年内把一篇推文变成了2亿美金的产品DAO](https://mp.weixin.qq.com/s/bZ98QgyWU3MT9Rw08SVHpw)
  - Amagi： 几周前，PartyDAO拿到A16z领投的1640万美金融资，估值两亿美金，这是迄今为止社区驱动类DAO的最高估值。让我们看看他们走过的历程。
- [链数查 - 链上数据平台](https://lianshucha.com/)
  - Caos： 链上数据平台，涵盖了多个网络的多种指标，图表种类丰富, 分类详细，帮助我们更好的查看链上数据
- [BLS12-381 write up](https://hackmd.io/@benjaminion/bls12-381)
  - Dream： 这篇科普文写的很全面， 介绍了BLS12-381 motivation，curve，paring, field extension 等


## 914

- [性能持续分析工具](https://pyroscope.io )
  - Mark Tang： Pyroscope是一个开源的持续性能分析平台，它通过持续分析的概念帮助各种应用接入到它的分析平台，提供更好的debug体验
- [免Gas费的交易](https://medium.com/coinmonks/gas-free-transactions-meta-transactions-explained-f829509a462d)
  - 北美开吻： 实际上是使用第三方Relayer来发送客户交易并支付gas. 并没有免费, 只是羊毛出在猪身上而已. 不过也是挺有意思的. 有兴趣的可以深入阅读.
- [Why rollups hit the sweet spot](https://dinoeggs.substack.com/p/why-rollups-hit-the-sweet-spot)
  - Rain： 在本文中，作者分析了区块链 的properties /tradeoffs，并提供了一种简单的理解rollups的方法。

## 913

- [Eth 2.0 的共識層和執行層分工及 The Merge 影響](https://medium.com/taipei-ethereum-meetup/eth-2-0-cl-el-separation-and-impact-of-the-merge-dbeb6828c907 )
  - Qi Zhou： 该文章系统阐述了 以太坊 The Merge 之后，现在的执行层(比如 Geth）和共识层（beacon chain）如何一起工作。
- [a useful tool for analyzing eth mainnet tx](https://ethtx.info/mainnet/0x4096e88107bd14522dfc3500325bff580728504580f5103d8247a2a32425889f/ )
  - dream： 比一般浏览器看 trace 和 token transfer 更方便
- [bulletproof-react](https://github.com/alan2207/bulletproof-react )
  - amagi： 如果你不知道项目该遵循哪些实践，可以看看下面这个仓库 他提供了一个示例项目，用来演示一个「简洁、可扩展、生产可用的项目架构」应该遵循的各方面实践，比如：- 测试如何做 - API层如何处理 - 性能问题怎么处理 - 安全问题怎么处理

## 912

- [TypeScript 类型挑战题库](https://github.com/type-challenges/type-challenges/blob/main/README.zh-CN.md)
  - Harry： 刷 TypeScript 类型挑战题库，少出 bug。
- [bulletproof-cairo](https://github.com/Lev-Stambler/bulletproof-cairo)
  - sny： Cairo 语言实现的 bulletproof 零知识证明协议, 完美体现了零知识证明算法之间的 “可组合性”.
- [解构合成资产赛道的设计与未来](https://mp.weixin.qq.com/s/bXN5MQdslDfdFKd1MCX52A)
  - 昊暠skyh： 解构合成资产赛道的设计与未来


## 911

- [TWAMM研究集](https://mirror.xyz/0x70626a.eth)
  - twpony： TWAMM，时间加权自动交易做市商机制。Paradigm的一个内部项目，旨在把大额交易订单，在一定的时间域内，拆成小的订单来成交，以减少滑点和对市场价格的影响。这个地址记录了团队每月TWAMM的研究成果。
- [公共治理的研究网站](https://metagov.org/)
  - Survivor： 关于公共治理的研究网站，有许多研究成果。提出了许多不错的探索课题，例如模块化政治，以开放组合系统方式建设社区治理体系。
- [MEV Memoirs: Into the Arena - Chapter 1, Part 1](https://noxx.substack.com/p/mev-memoirs-into-the-arena-chapter)
  - CyberOrange： 写EVM DEEP DIVES的作者又开坑写MEV系列了，感兴趣的可以跟进研究。


## 910 

- [footprint一站式可视化数据分析服务](https://www.footprint.network/)
  - Sam：提供一站式可视化数据分析服务，适合小白玩家，无需具备 SQL 技能，普适性更强，简单的拖放操作快速完成项目的数据分析，目前支持查询近20条链的数据。
- [Venture Capital to Innovation Capital](https://mhdempsey.substack.com/p/venture-capital-to-innovation-capital)
  - Koi：介绍了风险投资在crypto领域里的变化，以及 NFT 基金的创新点。
- [源码解读：EIP-4907](https://www.8btc.com/article/6763322)
  - River：这是一篇关于可租赁 NFT 标准“EIP-4907”的解读文章，梳理了“EIP-4907”是如何实现 NFT 租赁的

## 909

- [L2BEAT-L2扩展分析网站](https://l2beat.com/)
  - Caos： L2BEAT 是一个关于以太坊第二层（L2）扩展的分析和研究网站。他们从数据层面和风险层面对目前可用的各种以太坊 L2 系统进行了比较。
- [MPC Wallet](https://zengo.com/dive-into-multi-party-computation-mpc-technology-and-its-key-management-in-blockchain-applications/)
  - Collin： 介绍多方计算技术及其在区块链应用中的密钥管理
- [LSD危害及规避方案](https://notes.ethereum.org/@djrtwo/risks-of-lsd)
  - 陈锡亮： LSD的危害以及规避危害的方案

## 907

- [进化速度恐怖，社区规模最大的DAO：BanklessDAO](https://mp.weixin.qq.com/s/XxaUvovroow_Gzmq3fNexA )
  - Skyh： Bankless DAO 的运行方式，学习第一大 DAO 的运营模式
- [Solana 审计和安全资源](https://github.com/0xsanny/solsec )
  - Lucas： 该库收集了大量solana审计和安全资源，用于研究 Solana 智能合约安全、审计和漏洞利用的资源集合，对于学习开发与审计solana合约有极大帮助。
- [什么是 zkEVM（零知识以太坊虚拟机）?](https://mp.weixin.qq.com/s/DJfS2-x7lzpfS11hwxNJvQ)
  - 张晓——冰链科技： 一篇不错的文章，介绍了零知识证明以太坊虚拟机的基本知识。


## 906

- [sandwich机器人](https://github.com/libevm/subway)
  - 昊暠skyh： 今天Github的trending，以太坊三文治机器人（一般发出就没啥利润），可以拿来学习。
- [以太坊教程（来自chainfeed推荐）](https://speedrunethereum.com/)
  - 昊暠skyh： 教程从构建简单 NFT、去中心化质押、令牌发行、骰子游戏、DEX、多签钱包、SVG NFT 和预言机等 8 个实际用例入手，为有编程基础的程序员提供图文和代码示例。
- [一些有意思的NFT用例](https://techflowpost.mirror.xyz/DhNrPP1kTqqQUDM1n9ZDICcNMVBR_8Onj9sIWVknkMo)
  - 昊暠skyh： 一些有意思的NFT用例， 包括位置的 IRL ，奖励忠诚的用户，奖励数字/实物商品，IP 证书， NFT 投资组合，AI + NFTS等。


## 905

- [Things You Should Know About Databases (数据库必须知道的那些事)](https://architecturenotes.co/things-you-should-know-about-databases/)
  - Aaron Chi： 在这篇博客中作者比较详细的介绍了数据库的各种概念：如大家熟悉的索引，平衡树，还有在SSD，HHD上的优化手段等等
- [Warp的问题存储库](https://github.com/warpdotdev/Warp)
  - Zhangxuesong： Warp 是一个速度极快的现代化终端，目标是提高团队的工作效率。喜欢尝试不同的终端的小伙伴值得体验一下。
- [查询以太坊上某笔交易是否曾经包含在叔块中](https://github.com/metachris/eth-was-tx-uncled)
  - twpony： 小工具，通过交易哈希值，查询某笔交易是否曾经包含在叔块中。


## 904

- [Centralization Risks for Post-Merge Ethereum](https://research.thetie.io/centralization-risks-for-ethereum/)
  - CyberOrange：Merge即将到来，除了对其带来的好处而激动，我们也不能忘了提前预估可能的负面因素，并早作准备。
- [AI 生成工具Majesty Diffusion](https://github.com/multimodalart/majesty-diffusion)
  - River：Majesty Diffusion 是通过AI完成从文本到图像的实现。
- [Animated Drawings](https://sketch.metademolab.com/)
  - River：一款很有趣的 AI 工具，可以借助人工智能技术，让儿童绘画作品动起来。


## 903

- [Twitter 上有哪些 Web3 和加密相关资源值得关注](https://twitter.com/VittoStack/status/1542169491546808320)
  - Gala： 作者分享了他 2022 年最喜爱的 Web3 学习资源，涉及 Web3 开发、稳定币、市场等。
- [Polygon 和 Fantom 正遭受的 RPC 攻击](https://twitter.com/officer_cia/status/1542829400143568897)
  - Gala： Polygon 和 Fantom 正遭受 RPC 相关攻击，用户会看到 RPC 错误提示并被引导输入钱包助记词，这可能是 DNS 劫持或是一种形式的供应链攻击，作者还介绍了对应反制手段。
- [谁会成为下一个陷入流动性危机的大型机构？](https://research.huobi.com/#/ArticleDetails?id=279)
  - Gala： Amber Group 和 Wintermute Trading 值得关注，他们是无抵押贷款市场（Maple Finance 和 True Finance）上最大的借款人，贷款总额分别为 1.613 亿美元和 1.636 亿美元。


## 902

- [Crypto logo 图标库大全](https://cryptologos.cc/)
  - Caos： Crypto logo 图标库大全，可以导出， png svg 格式支持
- [详解借贷协议Maker、Aave、Compound的风控机制](https://www.jinse.com/blockchain/1789373.html)
  - Collin： 文章从抵押率、清算线、安全模块、预言机方面分析Maker、Aave、Compound三个借贷协议风控机制。
- [一个有趣的，使用 De Bruijn 序列实现 solidity log_2 函数的例子](https://gist.github.com/fiveoutofnine/cacd84649b836828a705d5d34e0296fe)
  - Amagi： 一个有趣的，使用 De Bruijn 序列实现 solidity log_2 函数的例子

## 901

- [加快你的英文文章阅读速度](https://www.jiffyreader.com)
  - Mark Tang： Jiffy是一个浏览器插件，通过将英文单词的前几个字母加粗，从而提升阅读速度
- [Not boring capital: Web3 Use Cases -- The Future Will web3 justify the hype?](https://foresightnews.pro/article/h5Detail/7671)
  - 罗不思： Not Boring Capital写的每一篇都值得看，我十分赞成“Web3 的快速迭代的核心是超结构”的概念，Web3 引入了创建软件基础设施的机会，这些基础设施可以永远免费使用，任何人都可以在其基础上进行建设，但会奖励 「建设者和参与者创造和贡献这些宝贵的系统，在未来许多年内为整个社会服务」
- [World's fastest password cracker](https://hashcat.net/hashcat/)
  - JinhuiFan： World's first and only in-kernel rule engine


## 900

- [以太坊漫游指南（中篇）](https://mp.weixin.qq.com/s/RtsVs2tSKPikMsXocXZAzQ)
  - 张晓： 以太坊漫游指南（中篇）
- [早期Web3创业公司融资入门教程](https://news.marsbit.co/20220623232157701602.html)
  - Qi Zhou： 早期Web3创业公司融资入门教程，话题包括启动资金的渠道、种子轮及后续轮的考量
- [Javascript to WASM](https://github.com/Shopify/javy)
  - Frozen： Shopify 提供的 javy 能够将 javascript 转换成 WASM 代码，并在 WASM runtime 中运行

## 898

- [还原Defi Hack交易](https://github.com/SunWeb3Sec/DeFiHackLabs)
  - twpony： 这个项目通过Foundry还原了32个Defi Hack事件的经过，在Fork区块链真实数据的前提下，Hacker的每笔交易都被呈现出来。
- [How Bridges Can Be the Next Big Risk in Crypto](https://blockcrunch.substack.com/p/how-bridges-can-be-the-next-big-risk)
  - Aaron Chi： 本文介绍了原生验证、本地验证、外部验证和 Optimistic 跨链桥四种类型跨链桥的技术原理，并从资金安全角度分析了各类跨链桥的风险等级。
- [智能合约安全审计入门篇 —— delegatecall (2)](https://mp.weixin.qq.com/s/6sAqyjv4gI3c9DzqoYhybA)
  - NaNa： 如何通过delegatecall 函数攻击和合约详解，以及对于开发者和审计者的修复建议

## 897

- [区块链行业的反身性](https://mp.weixin.qq.com/s/ntvFLqaxtIGpUrClw8Yt1A)
  - Koi：用索罗斯的反身性理论解释了牛熊市和死亡螺旋现象产生的原因。
- [The Fuel for Fast Execution](https://maven11.substack.com/p/the-fuel-for-fast-execution)
  - CyberOrange：近期Fuel推出了一个UTXO-based的DEX，让大家重新思考UTXO作为通用编程模型的可能，Maven这篇研报有助于更深入地理解fuel。
- [区块链导航](https://www.dexnav.com/)
  - Sam@CryptoMeta.ai：目前市面上比较好用的区块链聚合导航，收集了各类常用的工具/媒体/科学家工具。


## 896

- [Arbitrum奥德赛](https://www.panewslab.com/wap/zh/articledetails/tx1ts8w5.html)
  - Mark·R： 相信很多玩家都玩过马里奥奥德赛，现在，Arbitrum的奥德赛也来了，这是成为收集达人的游戏攻略。
- [实用教程：如何构建一个高度参与的社区？](https://twitter.com/Abbasshaikh42/status/1539982937545814016)
  - Gala： 构建适当正确的激励措施；定义社区效用，阐述如何为社区创造价值；了解社区成员的参与动机以创造内部社区结构；根据愿景衡量社区希望实现的目标。
- [复盘 Harmony 以太坊跨链桥 Horizon 遭遇攻击过程](https://twitter.com/BenWAGMI/status/1540199742013337603)
  - Gala： 黑客在交易中调用了多签钱包的 confirmTransaction() 方法，对传入的 transactionId 增加 confirmations 并调用 executeTransaction() 来执行交易，合约在进行 isConfirmed 时当时仅需要两个验证者节点可以执行。

## 893

- [Boto](https://boto.io )
  - Shooter： 零门槛构建 nft、uniswap 新池子、opensea、snapshot 的预警机器人推送到 discord、telegram、email 等
- [Solend DAO]
  - 7sf3tcWm58vhtkJMwuw2P3T6UBX7UE5VKxPMnXJUZ1Hn, Qizhou： solend 关于如何应对 SOL 巨鲸的巨额抵押物产生分歧，有社群指出通过投票没收巨鲸的巨额抵押物违背去中心化思想。有关 DAO 投票在https://realms.today/dao/
- [寻找 Web3 的灵魂](https://mp.weixin.qq.com/s/RAmXc3MRW2xfMVO_3uNGew )
  - Collin： 文章对灵魂绑定的代币 (Soulbound Token)不可转让性、抗审查的等应用场景的讨论

## 892

- [一个用于EVM系智能合约开发的组件库](https://github.com/Rari-Capital/solmate)
  - Zhangxuesong： 一个用于EVM系智能合约开发的组件库，在gas优化上值得我们参考学习。由 @paradigm 的研究工程师牵头开发
- [算法稳定币失败案例分析](https://mp.weixin.qq.com/s/SpaNQch6pz7Vs6yweueFlQ)
  - shooter： 本文通过列举现实世界中货币挂钩历史的失败案例，科普了加密货币稳定币的类型，进一步分析探讨稳定币尤其算法稳定币的风险所在。
- [PoS 不具备客观性](https://www.btcstudy.org/2022/06/16/proof-of-stake-is-not-objective/)
  - CyberOrange： 虽然在当下重提PoS的主观性假设已有点不合时宜，但这个逻辑论述仍值得一看。

## 891

- [BTC study学习网站](https://www.btcstudy.org/)
  - Zhangxuesong： 一个学习 BTC 底层基础知识的网站。内容都是精心整理和编辑的，很适合熊市的净心良药。
- [AAVE资金流向的可视化例子](https://twitter.com/jameskbh/status/1536484058833817602?s=20&t=qM5ItmtqK2BibV4hUhrTXw)
  - twpony： 基于dune和flourish studio开发的分析AAVE资金流向的可视化例子，比如这个例子分析了6月13日当天从AAVE流出的每笔金额大于500万刀的USDC的最终去向。
- [Osmosis Beyond Coinbase? 2022有哪些前沿技术和新功能](https://mirror.xyz/infinet.eth/VGWSbyqc4aJhcXk3US_VRcaCFdywkF4KbAeP7P6zZWs)
  - NaNa： Osmosis 是Cosmos 生态迄今最为活跃的枢纽之一，主打AMM自主应用链。本文将讲述在web3.0领域上，Osmosis较为独特及出彩的设计。

## 890

- [12个区块链游戏开发工具](https://twitter.com/0xshawnpang/status/1537964873543364608)
  - Harry：Shawn 整理了一个 blockchain game development tool 的名单然后做了简单分析和 popularity 排序。
- [Condensed Guide for Learning Web3 Development in 100 Days](https://tranqui.xyz/posts/100-days-of-code)
  - River：100天从编程小白到 Web3 全栈开发者的精华指南。
- [JSON Hero](https://github.com/jsonhero-io/jsonhero-web)
  - River：JSON-Hero 通过简洁的UI界面，让查阅和理解JSON数据变得更简单。


## 889

- [一款自动生成单元测试的 IDEA 插件，开发效率提升 70% 以上！](https://mp.weixin.qq.com/s/n-rt89KWA-wfZyQx6dML0w)
  - Joe： 这是一款java语言自动生成单元测试的插件，亲测使用非常方便，感兴趣的自己试试。
- [Dune Analytics教学](https://ournetwork.mirror.xyz/gP16wLY-9BA1E_ZuOSv1EUAgYGfK9mELNza8cfgMWPQ)
  - Koi： 一个适用于初学者的链上数据分析免费课程（主要运用dune & sql），链接里有课程的youtube recording和课程讲义。
- [Danksharding 与 MEV 设计：以太坊扩容终极解决方案](https://www.chainfeeds.xyz/feed/detail/4276a7fa-83d4-4242-9ca4-937b1604b8d1)
  - Gala： 对以太坊扩容的终极解决方案进行了梳理，提及很多前沿的技术以及开放性的问题，内容涉及：DS（Danksharding）、PDS（Proto-danksharding）、Verkle Trees、MEV 设计、MEV Boost、以太坊合并后客户端和共识的变化。

## 888

- [Maker 提案取出D3M 流动性提案](https://forum.makerdao.com/t/signal-request-set-the-aave-d3m-dc-to-0-zero/15775/14)
  - 陈锡亮： 针对当前市场环境，Maker 团队对 Aave stETH 进行了风险分析评估，最后决定提案要求提取所有Maker通过D3M机制直接注入Aave 的所有DAI流动性以降低风险。
- [Desmos Classroom](https://www.desmos.com)
  - Caos： 可视化计算平台，根据参数和动画效果可以看到实时演算的过程。支持科学计算，图形计算，公式分享，算法与公式推演必备工具。
- [5步开始你的web3职业生涯](https://newsletter.banklesshq.com/p/hire-job-career-crypto?s=r)
  - Amagi： 5步开始你的web3职业生涯

## 887

- [英文阅读浏览器插件](https://relingo.net/ )
  - Mark Tang： 英文不是很好，对阅读英文文章和视频比较感兴趣的小伙伴可以用一下，支持根据你目前的水平来为你标注可能超出你词汇的生词，你可以选择添加生词和标记已认识来操作
- [Go 语言高性能编程](https://geektutu.com/post/high-performance-go.html )
  - 0xMax： 对高性能 go 程序的编写，调优，测试，给出了全面的框架，推荐开发性能敏感 go 软件的开发者学习
- [王川: 论熊市投资者的自我修养](https://www.panewslab.com/zh/articledetails/sy1810iq.html)
  - JinhuiFan： 熊市，灵活就业人员除了跑美团滴滴外，应继续提高自我修养~

## 886

- [iota 各类研究文章](https://www.iota.org/foundation/research-papers )
  - Lucas： 该网页展示了 iota 链下的各类研究文章，其中有大量文章详细介绍了 DAG 链和传统链式区块链的各方向对比，对于理解DAG区块链与链式区块链有极大帮助。
- [Where to use a blockchain in non-financial applications?](https://vitalik.ca/general/2022/06/12/nonfin.html )
  - Qizhou： 这篇文章就在非金融应用程序中使用区块链在什么地方有意义进行了探讨。我们是否应该走向这样一个世界：即使是去中心化的聊天应用程序，每条消息都是包含加密消息的链上交易？或者，区块链只对金融有利？
- [密码学证明系统入门](https://mp.weixin.qq.com/s/ZH-u2fzx5owB8LpmKudm7Q )
  - 张晓——冰链科技： 零知识证明技术在计算机科学里所处的位置是可验证计算的一个分支，也即一种密码学证明系统。本文很好的介绍了该分支常用的几种数学工具，对建立零知识证明的直觉认识很有帮助。

## 885

- [Cobie 关于 Lido stETH 脱锚现象的思考](https://foresightnews.pro/article/detail/6613)
  - sny： Foresight News 翻译的 Cobie 关于 Lido stETH 脱锚现象的思考. 目前 Celsius 已经宣布暂停提现等业务, 非常应景.
- [获取 DeFi 数据的 subgraphs](https://github.com/messari/subgraphs)
  - Harry： Messari 搞的一个 subgraphs repo，包含了对各个 defi 项目数据的获取和分析，可以用于产品和分析，未来希望覆盖到每一个 defi 协议。
- [Solana扩容机制分析](https://www.chaincatcher.com/article/2075084)
  - 昊暠skyh： Solana扩容机制分析：牺牲可用性换取高效率的极端尝试 详细解释了solana扩容思路，共识算法和宕机原因
  
## 884

- [Issue #50: Trade Idea – Short stETH](https://maroonmacro.substack.com/p/issue-50-trade-idea-short-steth?s=r)
  - Aaron Chi： 作者简单易懂地介绍了stETH的来龙去脉（不会说的很繁琐），给出了自己的trade策略 - 抵押wETH，short wstETH，并历数了4个风险点，很直白易懂。
- [WEB5: AN EXTRA DECENTRALIZED WEB PLATFORM](https://developer.tbd.website/projects/web5/)
  - Survivor： Web3没在搞，惊现Web5, 定义了很多开源协议和组件，貌似还比较健全
- [2000 万 OP 代币被盗关键：交易重放](https://mp.weixin.qq.com/s/MjvCChjEGuSTr9cELQlW_A)
  - NaNa： 复盘OP被盗事件，认识交易签名构造以及交易重放。

## 883 

- [stETH带来的危机和机遇](https://mirror.xyz/liuyejinghong.eth/OIi_Hv17xcQR390AyYDtvz-RqQtZ-2P3n9fy5xXsFpw)
  - CyberOrange：stETH带来的危机和机遇，我认为这对于所有PoS协议都是一个问题，通过衍生代币套娃，将底层的安全性与上层的金融活动挂钩，可能会带来不可预知的后果。
- [一款免费强大的数学工具](https://www.geogebra.org/)
  - Sam：免费的课堂活动数字工具, 用于绘图计算, 几何作图, 白板协作等等。
- [蒙哥马利模乘法](https://en.wikipedia.org/wiki/Montgomery_modular_multiplication#Montgomery_form)
  - Dream：蒙哥马利模乘依赖于一种称为蒙哥马利形式的特殊数字表示。该算法使用a和b的蒙哥马利形式有效地计算ab mod N的蒙哥马利形式。效率来自于避免昂贵的除法运算。经典的模乘使用N除法减少双倍宽度乘积ab，并只保留余数。此除法需要商数估计和校正。相反，蒙哥马利形式依赖于一个与N互质的常数R>N，蒙哥马利乘法中唯一需要的除法是除以R。可以选择常数R，这样就可以轻松地除以R，从而显著提高算法的速度。实际上，R总是二的幂，因为二的幂除法可以通过位移位来实现。

## 881 

- [Web3.0 云计算平台](https://www.4everland.org/)
  - Caos： 基于 IPFS 和 Arweave 的 S3 兼容聚合器，也可以帮助你将前端网站部署到去中心化存储上，同时提供了 IPFS 网关和去中心化域名绑定的功能
- [介绍 Hybrid dApp 设计思路](https://medium.com/supercolony/hybrid-dapps-and-the-future-of-smart-contracts-b156eff402e4)
  - 陈锡亮： 本文通过对比传统应用是如何同时利用编译语言和脚本语言进行开发，和web3应用链原生功能和智能合约对比，分析了如何打造web3 hybrid dApp
- [a16z：给 Web3 项目的智能合约安全指南](https://mp.weixin.qq.com/s/h30t2n78yfevyN2zija9gg)
  - Collin： 本文概述了 Web3 开发人员和安全团队在设计、开发并维护智能合约时必须考虑的安全要素，覆盖了从威胁建模到应急响应准备的整个周期


## 880

- [为什么我害怕 the Merge](https://ethresear.ch/t/why-i-dread-the-merge-and-why-you-should-too/12740)
  - Qi Zhou： 为什么我害怕 the Merge
- [以太坊合并在即，关于PoS和PoW的4个常见误解需要澄清](https://mp.weixin.qq.com/s/3m2iu6qEpmY_Eg9EygZKTQ)
  - Nicole： 本文列出了大家对以太坊 PoS 的常见误解，并深层次阐述了PoS 共识机制的优势。
- [以太坊漫游指南：读懂以太坊发展路线图](https://mp.weixin.qq.com/s/FiiEBhC5dPEUP-LuB-X2Ew)
  - CyberOrange： 有关以太坊当下规划路线最综合全面的阐述。

## 878


- [让写代码 copy & paste 更简单](https://github.com/Goel25/ClonePilotExtension)
  - Harry： ClonePilot 是一个 VSCode 编辑器插件，只要你写一个函数名，它自动去 StackOverflow 搜索现成的代码，目前只支持 javascript。
- [解读 STARK、StarkWare、StarkEx、StarkNet 的区别](https://medium.com/starkware/starks-starkex-and-starknet-9a426680745a)
  - sny： StarkWare 官方文章解读术语的区别，值得阅读。
- [24个新项目，ETH Shanghai黑客松项目一览](https://www.theblockbeats.info/news/30760)
  - 昊暠skyh： 最近参加了hackathon，了解了下行业趋势，Soul bound和Social Fi挺受欢迎，特别自动生成avatar和AI动作捕捉，还有碳积分项目等

## 877

- [尝试绘制灵魂网络（soulweb）图：灵魂绑定代币（SBT）网络图概念和示例](https://mirror.xyz/lightninghsl.eth/lFUTzcbt6VigTb_aY0HJLniB0I_beX0B1n8BpyozvKA)
  - Survivor： Soul Bound Token和DeSoc的实践设计原型
- [Token 经济学：从理解用户想要什么开始](https://mp.weixin.qq.com/s/q6LdX1qIfs_qzXv0bWKIow)
  - Koi： 分别将协议与公司和国家进行对比，来探讨token经济学，并为token分析制定框架。
- [Tokengated Commerce (代币化的商业)](https://www.notboring.co/p/tokengated-commerce?s=r)
  - Aaron Chi： 《代币化商业》在这篇长对话中，Shopify员工、Social Capital前合伙人Alex Danco细致地谈了Shopify为商家与第三方开发者建立基础环境的尝试，为什么代币化的商业模式能够使品牌和社区之间的合作实现颠覆性创新，以及代币钱包作为支付手段如何激发人们的参与度。他强调加密技术的意义在于更大规模的合作，并详细解释了NFT及区块链上的其他智能合约如何实现可组合性（composability），从而变革社区商业、逐渐改变人们的行为。

## 875

- [如何构建和启动一个DAO？](https://mirror.xyz/rahuliyer.eth/0PaMd7kH-65pMzxgeiIQhn0blbIWwRWu3EbV-OZO1fA)
  - Gala： 本文探索了构建 DAO 需要的工具和方法，包括沟通工具、DAO 创建、DAO 治理、文档工具、财务管理、贡献者薪酬及簿记税收工具。
- [Web3 协议创收能力一览：商业模式决定收入天花板？](https://mp.weixin.qq.com/s/Cw4pdy9YhQxVWGflnCeCrA)
  - Gala： 文章分析了各种Web3协议，以及对应的营收能力。
- [详解Cosmos、Avalanche、BAS等五大多链架构的特点与优劣势](https://www.chaincatcher.com/article/2074911)
  - Gala： 文章针对五个独立的多链架构，详细研究并比较了这五个区块链架构在共享共识、容量和互操作性方面的取舍。

## 872

- [Visualising the 7-block reorg on the Ethereum beacon chain](https://barnabe.substack.com/p/pos-ethereum-reorg?sd=fs&s=r)
  - Qizhou： 关于以太坊最近的 7 块被 reorg 的深度分析
- [认证数据实验：Authenticated Data eXperiment](https://mp.weixin.qq.com/s/HF7bt0Dbom5hSFY4Z4dvyw)
  - 张晓： 个人言论与平台审核之间的矛盾一直很难调和。Web3 独辟蹊径，通过将数据层与展示层进行解耦，很巧妙地解决了这个问题。Bluesky 的协议揉合了 DID、IPFS与自认证数据，未来的工作很有看头
- [解读 zk, zkVM, zkEVM](https://mp.weixin.qq.com/s/808jMXvIUqB973aVHrAzGQ )
  - msfew： 文章比较简单易懂地介绍了 zk 的原理, SNARK vs STARK, zkVM 的定义与指令集分类, EVM 适配性定义, zkEVM 难点与流派, 以及 zkVM 与 zkEVM 的对比, 适合初学者阅读.

## 871

- [下载 twitter spaces 音频文件](https://github.com/HoloArchivists/twspace-dl)
  - shooter： twspace-dl 是一个命令行工具，依赖ffmpeg，可以方便下载的 twitter spaces 音频文件。
- [哈佛区块链最新研究：NFT 2.0投资指南](https://mp.weixin.qq.com/s/hPMjkgLuuH7inPhdPwAloQ)
  - 昊暠skyh： 投资NFT前应该考虑的几个因素
- [Opensea 推出了 Seaport 协议](https://opensea.io/blog/announcements/introducing-seaport-protocol/)
  - Harry： 2022 年 5 月 20 日 opensea 的官方博客宣布他们推出了一个新的 web3 marketplace protocol，用于 nft 交易，核心合约开源，地址 https://github.com/ProjectOpenSea/seaport
  
## 870

- [S3E12 | STEPN创始人Yawn: 地质勘探，开餐馆，到 10亿美元的Web3杀手应用](https://www.0011.one/posts/S3E12)
  - Aaron Chi： Yawn Rong 是STEPN 的联合创始人。STEPN 是一款web3 的跑步应用，通过游戏化金融（GameFi）的方式让用户边跑边赚钱，并帮助减少碳排放。在 2021 年 12 月底正式上线 beta 版以来，迅速火爆全球，也获得了包括红杉和币安在内多家机构的投资。在本期节目中，Yawn 先是和我们聊了他之前在酒店、地质勘探、餐饮和建材行业多样的工作经历，接着讲述了自己从 2017 年进入币圈，开始做加密货币投资，遇到熊市低谷，一直到 21 年末开始 STEPN 创业的过程。他还复盘了 STEPN 的发展历程，包括早期用户获取和融资遇到的困难，也分享了他对游戏化金融和代币经济系统的深入思考。我们还覆盖了 web3 的很多方面，如监管、跨链、社区和社交等。
- [链上AI发展新进展：链上预编译的智能合约+图像处理](https://ethresear.ch/t/solidity-ai-demo/5455/8)
  - Survivor： ETH 侧链上 Solidity 智能合约中首次AI 演示。一张图片上传到运行在区块链上的去中心化社交网络中。图像上传到区块链文件存储后，预编译的 SmartContract 会使用 Resnet50 神经网络对其进行分析，如果其中包含猫的图像，则将其拒绝。Gas与加密算法（例如 ECDSA 签名验证）处于同一数量级。能够在 EVM 中每秒进行数百次 AI 预测。
- [以太坊合并，如何影响显卡和区块链行业？](https://mp.weixin.qq.com/s/Fj6itQmqJe5AbDW2btjhMA)
  - yan： 以太坊合并，如何影响显卡和区块链行业？

## 869

- [The Hitchhiker's Guide to Ethereum](https://members.delphidigital.io/reports/the-hitchhikers-guide-to-ethereum)
  - CyberOrange：有关以太坊当下规划路线最综合全面的阐述。
- [简要总结Frax的机制](https://proud-jumpsuit-f82.notion.site/FRAX-1faa585552a2400db22b18f029310bcb)
  - Sam：此文简要介绍了Frax算法稳定币的稳价机制、AMO以及ve治理机制。
- [GitHub Student Developer Pack](https://education.github.com/pack)
  - River：对于大多数学生来说，学习工具的成本可能是高昂的。这就是为什么我们创建了GitHub学生开发包的原因。

## 868

- [慢雾：29 枚 Moonbirds NFT 被盗事件溯源分析](https://mp.weixin.qq.com/s/ERD8_3bBN0wgKsRO_FlesQ)
  - Joe： 鱼网站盗取用户NFT资产一直在发生，该文章分析最近发生的 Moonbirds NFT 被盗事件，希望大家提高警惕，加强安全意识。通过该分析也可以了解攻击者操作手法，感兴趣的可以看看。
- [ZK 漫谈：zkVM 的架构、技术方案及未来展望](https://yolonotes.notion.site/ZK-ZKVM-a2a6523fa7bd47d28ce469d3ac8d3807)
  - Gala： Starkware 技术转型最彻底，但用户学习门槛最高；而 Zksync 相对比较均衡，保留一部分 solidity 特性，发挥局部 ZK 性能。文章带你了解当前zkVM。
- [Bankless：元治理或成加密领域下一个趋势？](https://newsletter.banklesshq.com/p/ultimate-guide-to-metagovernance?s=r)
  - Gala： 创新在 DeFi 中并没有死亡，元治理代表了一种全新的 DAO 参与 DeFi 治理的方式。通过利用元治理代币来利用其治理能力，DAO 能够以更低的成本追求其战略目标。

## 867

- [反向词典WantWords拯救词穷的你](https://mp.weixin.qq.com/s/er-JwST7dUQjMh6VzBE1bA)
  - Collin： 所谓“反向”，就是和常规词典不同，不是按词寻义，而是反过来你给词典一段描述让它来帮你找词语或成语来拯救了失语的我们。微信小程序搜索“WantWords”（曾叫做“万词王”）添加小程序体验吧。
- [raycast](https://www.raycast.com/)
  - Caos： macOS 启动器，Alfred 的优雅替代，支持用 Js 定义自己的插件整合大部分应用与服务
- [STEPN设计思路和经济模型的分析](https://mirror.xyz/0xpineapple.eth/Mrb0Yzj4DK0g_xQzaevKqMQ7YkrWDOVNZyjzyep7csQ)
  - Amagi： 最近的连续一个多月，作者通过日更的33篇STEPN数据分析日记，对这个从新兴到马上将要站上Game-fi龙头地位的游戏有了更深的认识。结合了小蓝的《关于P2E游戏经济的十条建议》，作者希望能用自己对STEPN游戏的浅薄理解，来分析下STEPN的设计思路和经济模型
  
## 866

- [Web3 身份生态系统](https://web3caff.com/zh/archives/11687)
  - 昊暠skyh： 这篇详细描述了DID身份相关项目，一些现有工具和方案。原文：https://mirror.xyz/dommy.eth/YsZmPZUxs4LsS-sS0APg25LJiFxOBnCibj2gzXFX98c
- [Solana Cookbook](https://solanacookbook.com/)
  - Lucas： Solana Cookbook是在Solana 上开发应用程序的基本概念和参考参考。每个概念和资料都将为 Solana 开发的特定方面提供具体实践细节，同时提供额外的资源和使用示例。是入门solana非常好的资料！
- [一款国产的区块链行业的 hao123](https://linkroad.im)
  - Zhangxuesong： 一款国产的区块链行业的 hao123, 自己打开套索吧. 内容还挺丰富的。


## 863

- [慢雾：Discord 私信钓鱼手法分析](https://mp.weixin.qq.com/s/eGieV1P_inrwubwhsWutwg)
  - NaNa： Discord 私信钓鱼手法分析
- [Polkadot v1.0：分片和安全（4）—— 争议的处理](https://mp.weixin.qq.com/s/UoaOtid6Sbk5e5Wad9q44w)
  - https://mp.weixin.qq.com/s/UoaOtid6Sbk5e5Wad9q44w
- [Messari：详解Oasis Network运作机制、经济模型与路线图](https://www.chaincatcher.com/article/2072039)
  - NaNa： 凭借其独特的架构和隐私计算技术的部署，Oasis Network 正在为发展一个开放的、对数据负责的隐私经济播下种子。


## 862

- [Providing easy access to human-friendly Web3 data](https://github.com/NaturalSelectionLabs/Unidata)
  - Zhangxuesong：Web3 的美妙之处在于每个人都拥有自己的数据，但访问和显示自己的数据是一件困难的事情。每个应用程序都有自己的数据规范和访问方式，开发者很难一一适应。Unidata 从各种平台和数据源获取数据，将其转换为相同且易于使用的规范，并为开发人员提供统一的 API 来访问它。Web3 开发从未如此简单。    
- [The Dark Side of Decentralization](https://mirror.xyz/kaylaphillips.eth/Rg2NAn-FngPrYluXPdK9JmMQCIjUlDeOXTrUlf77MHA)
  - Zhangxuesong：文章阐述了 Web3 并不是解决 Web2 中所有问题的灵丹妙药。相反，它在媒体和通信、治理和社会秩序等方面可能会出现更多问题。建议我们对 Web3 的未来保持理性的乐观。
- [Decentralized Society: Finding Web3's Soul](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4105763)
  - KOI：Illustrate how non-transferable “soulbound” tokens (SBTs) representing the commitments,credentials, and aliations of “Souls” can encode the trust networks of the real economy to establish provenance and reputation. 

## 861

- [你不可不知道的 Web3 第一原则](https://mp.weixin.qq.com/s/nF8dAPnuMERxVdqSv04UZg)
  - Gala： 文章从设计的角度来了解Web3的技术和产品浪潮的本质，试图推导出一些简单、清晰、易懂的首要原则。
- [写在以太坊“大合并（The Merge）”前夜](https://mirror.xyz/0x17f3F81860345567482E1D232FB5B6f8bD77f3Bd/wnSmHAUWeBOjs2O56R6WaV_TPH08UGB5_aHakC4acso)
  - Gala： V神在ETH上海的直播，作者分享一些新的见解。
- [自建 Git 服务器：Gitea 与 Gitlab 部署踩坑经历与对比总结](https://zhuanlan.zhihu.com/p/486410391)
  - Shooter： 对于这两个被广泛使用的可自托管的 Git 托管方案，网上似乎都是几张清单式的功能对比表，所以想在这篇文章中对比两者在实践中感受到的的差别，以及说说趟过的坑，为准备自建 Git 托管网站的用户提供参考与建议。

## 860

- [连接web验证wifi小帮手](http://neverssl.com)
  - 陈锡亮： 出行公共wifi连上了但是怎么都没有网怎么办，大概率是公共wifi会有专门的登陆页面没有正常显示，路由自动劫持所有请求显示登录页面，但是https会判断这是非法劫持不会显示，导致网络怎么连都连不上。neverssl提供了一个永远都不会启动https的网站，如果连不上的话可以尝试打开neverssl页面来判断是否是路由劫持了页面或者就是wifi本身有问题。
- [程序员 side project 指南](https://sideproject.guide/)
  - Caos： 程序员 side project 指南，帮助你从想法到实现的一些指引和经验总结，涵盖了想法验证，快速实现，获取用户和如何赚钱等等工具与心得
- [RSS Aggregator for Web3](https://mirror.xyz/chainfeeds.eth/mwgMtCDcz5_YW-dRacfD2pzsb_gMUSyal9M5nR5472A)
  - Amagi： 作者决定把 Web3 信息流聚合的「武功秘籍」开源出来，任何人都可以构建个性化的订阅工具。

## 858

- [通过咬牙的声音做身份识别](https://www.unite.ai/biometric-authentication-by-grinding-your-teeth/)
  - Harry： 最近来自美国和中国的两篇研究论文提出了一种基于牙齿的身份验证的新颖解决方案：只需稍微磨牙或咬一下牙齿，就可以生成有效的生物特征，可以作为身份识别的依据。实验表明，平均认证准确率为 98.6%，并且可以抵抗 98.9% 的欺骗攻击。不知道这种方式是不是可以用在 web3 的身份上，每个人链上存一个咬牙的声音数据作为身份证据，缺点是换牙的时候需要重新认证。
- [全景解析多链格局下的跨链赛道](https://mirror.xyz/copuppy.eth/J_5_Rt60cTbPc0o0RwkB07bpGzFDQQPD2to8gehfVC4 )
  - 昊暠skyh： 该文章描述了一些多链下跨链协议和方案，和一些现有项目的应用场景（居然没有 DOT
- [500 亿美元的烟花秀：详解 Luna 的过去，现在和将来](https://mp.weixin.qq.com/s?__biz=MzA4MzE1MzQ3MA==&mid=2450143799&idx=1&sn=e7975c8557badf03cb75d5da8567de9d&scene=58&subscene=0 )
  - Nicole： 推荐一个播客节目，主要讲述了 Luna 和 UST 的过去现在和将来，以及稳定币是怎么稳定的；Luna 的崩盘对其他 Layer 1 和 市场监管的影响。

## 856

- [Build A Disk based Log Structured Hash Table Store](https://github.com/avinassh/py-caskdb)
  - Aaron Chi： 这个github repo用python展示了如何创建一个基于硬盘的KV结构数据库。项目面向初学者教学用，不是面向生产环境。
- [加密时代的失败承诺](https://mp.weixin.qq.com/s/fIarRRQ0kKwtYwDxi0Eudw)
  - Survivor： 加密时代不应该只追求内部收益率，文章还是有些独到之处。
- [跨链通信的危险、现在和未来：Layer Zero之后的道路](https://mirror.xyz/0xaCc261f95c552386Da8350a6FffE3A0b9Ca67E0B/nJSgiRICtYqtP-aJpJ39BSgEg7bymvMquyL_H-o9tZ0)
  - NaNa： 跨链设计领域还没有明显的赢家，本文主要从成本、安全性、效率三个方面比较了所有主流的跨链桥设计。


## 855

- [The economics of rollup fees](https://www.alexbeckett.xyz/the-economics-for-rollup-fees/ )
  - CyberOrange：通过对Rollup交易手续费的构成进行分析，可以看出Rollup相比L1便宜了很多，但是仍有较大的局限性，为了突破这个局限，需要安全的数据可用性层。
- [Solidity Best Practices for Smart Contract Security](https://www.theblockbeats.info/news/30542 )
  - River：本文是 Metamask 项目方（Consensys）在 2020 年 8 月写的一篇博客，关于智能合约安全，其中给了 Solidity 程序员 16 条安全建议，并包含代码样例。
- [JSON 工具 ](https://github.com/jsonhero-io/jsonhero-web)
  - River：该项目提供了简洁直观的 UI 界面，让查阅和理解 JSON 数据变得更为轻松便捷。

## 854

- [Web3 DApp最佳编程指南](https://guoyu.mirror.xyz/RD-xkpoxasAU7x5MIJmiCX4gll3Cs0pAd5iM258S1Ek)
  - Skyge： 推荐一些涉及到开发一个 DApp 所涵盖的几乎所有方面的工具，正如文中所讲，是"Web3 DApp 最佳编程指南"。
- [OpenLaw：基于ETH智能合约的法律合同签署工具](https://www.openlaw.io/)
  - Shooter： OpenLaw 是一个能够创建「智能」法律协议的项目，为所有人提供了一种简单的方法来引用和触发基于以太坊的智能合约来管理合约承诺。OpenLaw 提供3大功能：合同的在线编辑、合同的在线签署、智能合约的集成。
- [ZK身份: 为什么需要及怎样做到？](https://www.ethereum.cn/Technology/zk-id-1)
  - Gala： 传统的数字身份存在哪些问题？密码学为数字身份系统打开了哪些设计空间，且身份到底包含哪些内容

## 853

- [Horizon UI](https://horizon-ui.com)
  - Caos： Horizon UI， 建立在 Chakra UI 之上的开源管理模板，有 70 多个深色/浅色前端单独元素，比如：按钮、输入、导航栏、导航选项卡等等，其中还包含 NFT 市场的 UI 设计效果。
- [算法稳定币行业研究](https://mirror.xyz/0xE8C078C600132AB68685eA06a5f7C7bC1A349b76/5zSuoGVgDtkod_Qs-WbmmHLNsVno6EL8ChwDnyAYdHw)
  - Collin： 文章介绍了三个算法稳定币Olympus、FRAX和近日崩盘的Terra的经济模型分析、实现原理、和实现模拟。
- [零知识证明与机器学习能碰撞出什么样的火花？](https://mp.weixin.qq.com/s/rFLUKcNvcvHaqzyXcBlPiQ)
  - 张晓-冰链科技： 文章中demo实现了一个简单的用于手写数字识别（MNIST）的计算机视觉深度学习卷积神经网络。

## 851

- [Rust 版本的 Electron](https://github.com/tauri-apps/tauri )
  - Survivor： 目前初期阶段，如果仅仅是包裹页面应用同时调用本地文件系统，那比近百兆的 Eletron 要小很多，大约 1Mb 左右。和任何前端页面完美结合，提供 npm 包，亲测好用
- [JSON 可视化工具](https://jsonhero.io )
  - MarkTang： 像 Mac Finder 一样的 JSON 查看器，JSON Hero 可以让你查看 JSON 数据时更加清晰
- [A tool for running Rust lints from dynamic libraries](https://github.com/trailofbits/dylint )
  - Lucas： 自己写动手写 Rust 代码规范，定义 lint。不需要在 Clippy 上进行分叉，dylint 让你可以更加便捷的编写一些 Rust 自定义 lint 集，强大的 Rust 中的代码分析工具。

## 850

- [零知识证明术语](https://nmohnblatt.github.io/zk-jargon-decoder/foreword.html)
  - sny： 网站比较全面和完整地介绍了零知识证明技术相关的术语和概念, 对初步学习 zk 有很大的帮助
- [Web3.0 通识课](https://j08v3n7cqq.feishu.cn/docs/doccnu8PLq5zJcaZJUzTaLyEOPc)
  - Harry： 对于 Web3.0 的各种知识和项目做了一个大概的介绍
- [MetaMask 推出 MetaMask Snaps](https://github.com/piotr-roslaniec/awesome-metamask-snaps)
  - shooter： MetaMask Snaps 是一个允许任何人安全地扩展 MetaMask 功能的系统，是 MetaMask 在隔离环境中运行的程序，可以自定义钱包体验。例如，Snaps 可以向 MetaMask 添加新的 API，添加对不同区块链协议的支持，或使用内部 API 修改现有功能。

## 849

- [Build web apps with nothing but Python.](https://anvil.works/)
  - Zhangxuesong： 一个只用 Python 就可以开发 web 应用的框架。即使自己没有任何web开发经验。
- [一文读懂 “Evm” “os”](https://mirror.xyz/0x0000003a2AC76e2FB5c046406900C481Dea72Acb/v_9AVOcOwAi58d8gtTG2ndoQNobF_MUH2vpCkEDyZlA)
  - NaNa： 深入了解一下EVMOS的世界，以及了解这对互操作性、跨链通信和模块化意味着什么。
- [Flash Boys 2.0: Frontrunning, Transaction Reordering, and Consensus Instability in Decentralized Exchanges](https://arxiv.org/abs/1904.05234)
  - Aaron Chi： 这篇论文里分析了在DEX上套利机器人的一些手法和模式（抢先交易，提高gas费用之类的），代码可以在(https://github.com/pdaian/flashboys2)找到。论文写于2019年。

## 848

- [V神：为什么权益证明棒棒的](https://vitalik.ca/general/2020/11/06/pos2020_zhTW.html)
  - Sam：V神比较了POS比POW更加安全的三个因素。   
- [如火如荼的「稳定币战争」](https://mp.weixin.qq.com/s/-3Z7WdMc3Z4zfuSNv73Dzg )
  - Koi：比较USDC、USDT、BUSD、UST、DAI、FRAX、FEI 和 OHM在dex、货币市场和跨链桥上的的流动性及其他应用场景。
- [zkEVM (二) --EVM Circuit介绍](https://mp.weixin.qq.com/s/3p9qAXtwXkjNrV3EEL56Vg)
  - Dream：EVM circuit遍历block证明中包含的交易，以验证交易的每个执行步骤是否有效, 交易中各个部分，包括签名， hash, 合约执行字节码都要进行约束&证明。

## 846

- [amsterDOT 2022 所有演讲视频](https://www.youtube.com/channel/UCMaNG562UUA2pdCqOhEAg1A/videos)
  - 陈锡亮： amsterDOT 是这两年来第一个大型线下波卡生态研讨会，其中有多个平行链项目和基础设施项目参与，分享各个项目最新的进展以及对波卡生态未来的展望
- [Web3核心工种一览，你适合哪一“款”？](https://www.8btc.com/article/6747530)
  - Collin： 文章基于Web3.0的特点和目前行业现状讨论未来个人在web3.0将获得哪些工作机会。
- [react debug 工具 tilg hook](https://github.com/shuding/tilg)
  - Amagi： supa 强的 react debug 工具 tilg hook，帮助你轻松看到 组件 render 次数 / 组件生命周期 / 添加 debug 信息 / 查看组件 state 和 props 的更改


## 844 

> https://mp.weixin.qq.com/s/ZoC5K2fSejUO1QczFSVuDg

## 843



