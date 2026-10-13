# DMIT 和 HostDare：同为美西 CN2 GIA，年付三十几刀和月付十几刀差在哪，附全套餐价格与超量策略对比

搜这两个牌子的人，卡点基本是同一个：HostDare 年付三十几美元就能挂上「CN2 GIA」这块牌子，DMIT 同档位要贵出一大截，多花的钱到底买到了什么？

这篇不讲品牌故事，只把两家的线路结构、套餐价格、超量规则和第三方实测数据摆出来，你自己判断值不值。

## 先给结论

两家卖的不是同一类东西。

HostDare 的核心竞争力是价格门槛。年付 $35.99 起步能拿到洛杉矶 CN2 GIA 回程优化，这个价位在市场上确实难找第二家。代价是入门带宽窄（30~50Mbps），去程线路不优化，高峰期部分节点丢包明显。

DMIT 卖的是线路完整度和超量后的兜底。三网去程回程都走优化线路，流量跑完不停机只限速，代价是价格。洛杉矶 Pro 系列入门月付 $12.98，年付档位的 Pro.WEE 是 $39.9。

一句话：预算决定一半，另一半看你能接受多大的晚高峰波动。

## 产品线速览：两家分档逻辑完全不同

DMIT 按**网络线路**分档，同一个机房卖三种网络：

| 系列 | 线路性质 | 面向谁 |
| --- | --- | --- |
| Premium（Pro） | 三网 CN2 GIA 回程，去程电信 GIA / 联通直连 AS4837 / 移动 AS58453 | 面向中国大陆的建站、跨境业务 |
| Eyeball（EB） | 三网 CMIN2 + AS9929 回程优化 | 移动、联通用户，性价比路线 |
| Tier 1（T1） | 国际 BGP，**不针对中国大陆做优化** | 海外业务、大带宽大流量 |

香港和东京机房同样是这三档。另外 DMIT 曾有一条 Premium Secure（LAX.sPro）高防产品线，官方通知该产品线于 2025 年 12 月 31 日正式停产，原因是与 Cloudflare 的合同提前结束——如果你看到有人推荐 sPro，那条线已经不存在了。

HostDare 按**硬盘类型**分档，线路只有一条主线：

| 系列 | 存储 | 线路 |
| --- | --- | --- |
| CSSD | Intel NVMe | 洛杉矶 CN2 GIA 三网回程 |
| CAMD | AMD NVMe | 同上 |
| CKVM | HDD RAID10 | 同上 |
| ASSD / SSD / HDD | NVMe / SSD / HDD | 洛杉矶普通线路，无中国优化 |
| JSSD / NKVM | NVMe / HDD | 日本（NTT / 软银） |
| BGSSD | NVMe | 保加利亚 |

选型逻辑其实很简单：**要回国快就看 CSSD / CAMD / CKVM，要便宜大带宽就看 ASSD 那一档，两者不要混着比。**

## 「CN2 GIA」在这两家不是一回事

这是最容易踩坑的地方。同一个词，两家的实现方式有差别。

DMIT 洛杉矶 Pro 系列的做法是全程优化：电信去程走 CN2 GIA（AS4809），联通去程直连 AS4837，移动去程经香港移动 AS58453，三网回程统一走 GIA（AS4809）。Eyeball 系列则是回程 CMIN2 + AS9929 组合，电信方向此前已改路由到联通 9929，用来缓解 CMIN2 晚高峰的跨网问题。

HostDare 的 CN2 GIA 系列主打三网回程优化（电信 CN2 GIA + 联通 CU / CUII + 移动 CMIN2），去程走普通出海直连线路。第三方对比文章直接点破了这个结构差异：去程不优化，意味着高峰期去程可能出现拥堵。V2EX 上也有多年老用户吐槽 HostDare 是「半程 GIA，有时候去程切其他的，有时候回程切」。

这条差别会直接体现在晚高峰。

## 第三方实测数据摆在一起看

下面这组数据来自第三方对比测评，用的是两家各自的入门套餐，测试工具和方法基本一致：

| 项目 | DMIT | HostDare |
| --- | --- | --- |
| 全国平均 Ping（洛杉矶） | 约 158ms | 约 170ms |
| 高峰期丢包 | 接近 0（<0.1%） | 个别节点 9%~10% |
| sysbench CPU（基础款） | 约 4164 events/s | 约 410 events/s |
| 磁盘平均 I/O | 约 899 MB/s | 约 1018 MB/s |

几个必要的注解：

- CPU 那一行差距看着夸张，但**不能直接当结论**。同一位测试者在原文里说明了，HostDare 用的是偏向性价比的基础套餐，换成 CAMD（AMD EPYC）系列后算力与 DMIT 相差不大。要比算力就比 CAMD，别拿 CSSD 的入门款去比。
- 磁盘这一项 HostDare 反而略快，不过 900MB/s 和 1000MB/s 在日常建站场景里感知不到差别。
- 延迟和丢包是最有参考价值的两行，因为它跟线路结构直接对应——去程不优化带来的波动，会出现在这里。

还有一份香港节点的实测报告给出类似趋势：DMIT 香港 CN2 GIA 电信延迟约 15ms、移动 18ms、联通 12ms，晚高峰丢包 0.3%；HostDare 香港节点电信 37ms、移动 42ms、联通 35ms，晚高峰丢包升到 0.9%。注意这份测试里两家香港机房的线路归属不同，不能直接套到洛杉矶套餐上。

## DMIT 当前套餐与价格

DMIT 的套餐数量很多（三个机房 × 多条线路 × 多个配置档），库存和硬件平台切换也频繁——LAX.AN4 已基本下线，新补货的是 AN5（AMD EPYC 9005），AS3（EPYC 7003）也会不定期回归，两者价格不同。下表是近期公开的主力档位，下单前请以官网实时显示为准。

**洛杉矶 LAX.Pro（CN2 GIA）与 LAX.EB（CMIN2）**

| 套餐 | CPU | 内存 | 存储 | 月流量 / 带宽 | 超量后 | 月付价格 | 购买 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Pro.TINY | 1核 | 2GB | 20GB | 1TB / 1Gbps | 4Mbps 不限量 | $12.98 | [查看 Pro.TINY 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.TINY | 1核 | 2GB | 20GB | 1.5TB / 2Gbps | 4Mbps 不限量 | $12.98 | [查看 EB.TINY 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| Pro.Pocket | 1核 | 2GB | 40GB | 1.5TB / 4Gbps | 4Mbps 不限量 | $18.90 | [查看 Pro.Pocket 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.Pocket | 2核 | 2GB | 40GB | 3TB / 4Gbps | 4Mbps 不限量 | $18.90 | [查看 EB.Pocket 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| Pro.STARTER | 2核 | 2GB | 80GB | 3TB / 10Gbps | 4Mbps 不限量 | $38.90 | [查看 Pro.STARTER 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.STARTER | 2核 | 2GB | 80GB | 5TB / 10Gbps | 4Mbps 不限量 | $38.90 | [查看 EB.STARTER 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| Pro.MINI | 2核 | 4GB | 80GB | 5TB / 10Gbps | 8Mbps 不限量 | $76.90 | [查看 Pro.MINI 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.MINI | 4核 | 4GB | 80GB | 10TB / 10Gbps | 8Mbps 不限量 | $76.90 | [查看 EB.MINI 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| Pro.MICRO | 4核 | 4GB | 160GB | 7TB / 10Gbps | 8Mbps 不限量 | $99.90 | [查看 Pro.MICRO 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.MICRO | 4核 | 4GB | 160GB | 14TB / 10Gbps | 8Mbps 不限量 | $99.90 | [查看 EB.MICRO 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| Pro.MEDIUM | 4核 | 8GB | 160GB | 14TB / 10Gbps | 10Mbps 不限量 | $219.90 | [查看 Pro.MEDIUM 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.MEDIUM | 6核 | 8GB | 160GB | 30TB / 10Gbps | 10Mbps 不限量 | $219.90 | [查看 EB.MEDIUM 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| Pro.LARGE | 8核 | 16GB | 320GB | 25TB / 10Gbps | 10Mbps 不限量 | $459.90 | [查看 Pro.LARGE 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| EB.GIANT | 12核 | 24GB | 640GB | 100TB / 10Gbps | 10Mbps 不限量 | $839.90 | [查看 EB.GIANT 当前价格](https://www.dmit.io/aff.php?aff=12046) |

年付还有更便宜的入门款：LAX.Pro.WEE（1核 / 1GB / 20GB / 800GB）$39.9/年，LAX.Pro.MALIBU（1核 / 1GB / 20GB / 1000GB）$49.9/年，这类年付款经常缺货，补货窗口很短。

**洛杉矶 LAX.T1（国际线路，大带宽）**

| 套餐 | CPU | 内存 | 存储 | 月流量 / 带宽 | 超量后 | 价格 | 购买 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| T1.WEE | 1核 | 1GB | 20GB | 1TB / 4Gbps | 100Mbps 不限量 | $36.90/年 | [查看 T1.WEE 当前价格](https://www.dmit.io/aff.php?aff=12046) |
| T1.V2C2G | 2核 | 2GB | 40GB | 5TB / 10Gbps | 100Mbps 不限量 | $14.90/月 | [查看 T1 大流量款价格](https://www.dmit.io/aff.php?aff=12046) |
| T1.V4C4G | 4核 | 4GB | 120GB | 20TB / 10Gbps | 200Mbps 不限量 | $36.90/月 | [查看 T1 大流量款价格](https://www.dmit.io/aff.php?aff=12046) |
| T1.V8C16G | 8核 | 16GB | 240GB | 80TB / 10Gbps | 500Mbps 不限量 | $119.90/月 | [查看 T1 大流量款价格](https://www.dmit.io/aff.php?aff=12046) |
| T1.G2C4G | 2核 | 4GB | 80GB | 4TB / 10Gbps | 100Mbps 不限量 | $16.90/月 | [查看 T1 综合性能款价格](https://www.dmit.io/aff.php?aff=12046) |
| T1.G4C8G | 4核 | 8GB | 160GB | 8TB / 10Gbps | 100Mbps 不限量 | $36.90/月 | [查看 T1 综合性能款价格](https://www.dmit.io/aff.php?aff=12046) |

**香港 HKG 与东京 TYO**

| 套餐 | CPU | 内存 | 存储 | 月流量 / 带宽 | 超量后 | 价格 | 购买 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HKG.T1.WEE | 1核 | 1GB | 20GB | 1TB / 4Gbps | 50Mbps 不限量 | $36.90/年 | [查看香港 T1 套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.TINY | 1核 | 1GB | 20GB | 2TB / 4Gbps | 100Mbps 不限量 | $6.90/月 | [查看香港 T1 套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.MINI | 2核 | 2GB | 60GB | 8TB / 10Gbps | 100Mbps 不限量 | $21.90/月 | [查看香港 T1 套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.TINY | 1核 | 1GB | 20GB | 500GB / 1Gbps | 按官方规则限速 | $39.90/月 | [查看香港 Pro 套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.STARTER | 1核 | 2GB | 40GB | 1TB / 1Gbps | 按官方规则限速 | $79.90/月 | [查看香港 Pro 套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.MINI | 2核 | 2GB | 60GB | 1.5TB / 1Gbps | 按官方规则限速 | $126.90/月 | [查看香港 Pro 套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.GIANT | 8核 | 16GB | 640GB | 6TB / 1Gbps | 按官方规则限速 | $500/月 | [查看香港 Pro 套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.WEE | 1核 | 1GB | 20GB | 1TB / 4Gbps | 50Mbps 不限量 | $36.90/年 | [查看东京 T1 套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.TINY | 1核 | 1GB | 20GB | 500GB / 1Gbps | 按官方规则限速 | $21.90/月 | [查看东京 Pro 套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.EB.TINY | 1核 | 1GB | 20GB | 1TB / 1Gbps | 按官方规则限速 | $25.90/月 | [查看东京 Eyeball 套餐](https://www.dmit.io/aff.php?aff=12046) |

香港 Pro 系列同时存在 AS3（EPYC 7003）和 AN5（EPYC 9005）两个平台版本，同配置价格有差异，购买时留意页面显示的处理器型号。

## HostDare 当前套餐与价格

**洛杉矶 CN2 GIA 系列**

| 套餐 | CPU | 内存 | 存储 | 月流量 / 带宽 | 价格 | 购买 |
| --- | --- | --- | --- | --- | --- | --- |
| CSSD0 | 1核 | 768MB | 10GB NVMe | 250GB / 30Mbps | $35.99/年 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CSSD1 | 1核 | 1GB | 25GB NVMe | 600GB / 50Mbps | $55.99/年 或 $10.99/月 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CSSD2 | 2核 | 2GB | 50GB NVMe | 1TB / 60Mbps | $85.99/年 或 $13.99/月 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CSSD3 | 3核 | 4GB | 100GB NVMe | 1.5TB / 80Mbps | $240.99/年 或 $29.99/月 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CSSD4 | 4核 | 8GB | 200GB NVMe | 2.5TB / 100Mbps | $70.99/月 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CSSD5 | 5核 | 16GB | 400GB NVMe | 3.5TB / 100Mbps | $115.99/月 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CSSD6 | 6核 | 32GB | 800GB NVMe | 5.5TB / 100Mbps | $190.99/月 | [查看 CSSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CAMD0 | 1核 | 768MB | 10GB NVMe | 250GB / 30Mbps | $45.99/年（近期常缺货） | [查看 CAMD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CAMD1 | 1核 | 1GB | 25GB NVMe | 500~600GB / 50Mbps | $32.99/季 | [查看 CAMD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CAMD2 | 2核 | 2GB | 50GB NVMe | 1TB / 60Mbps | $49.99/季 | [查看 CAMD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CAMD3 | 3核 | 4GB | 100GB NVMe | 1.5TB / 80Mbps | $105.99/季 | [查看 CAMD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CAMD4 | 4核 | 8GB | 200GB NVMe | 2.5TB / 100Mbps | $75.99/月 | [查看 CAMD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CKVM1 | 1核 | 756MB | 35GB HDD | 600GB / 50Mbps | $49.99/年 | [查看 CKVM 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CKVM2 | 2核 | 1.5GB | 75GB HDD | 1TB / 60Mbps | $76.99/年 | [查看 CKVM 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CKVM3 | 3核 | 4GB | 150GB HDD | 1.5TB / 80Mbps | $224.26/年 | [查看 CKVM 套餐](https://www.dmit.io/aff.php?aff=12046) |
| CKVM6 | 1核 | 756MB | 150GB HDD | 600GB / 50Mbps | $66.99/年 | [查看 CKVM 套餐](https://www.dmit.io/aff.php?aff=12046) |

退款/付款周期上有两个细节：CSSD1 只支持 Linux，从 CSSD2 起才支持 Windows；CAMD 系列从 CAMD3 起支持 Windows，同样需要自备授权。付费周期上，CSSD1~CSSD3、CAMD1~CAMD3 最低只支持季付，更高级别才开放月付。

**洛杉矶普通线路（ASSD / 便宜 SSD、HDD）**

| 套餐 | CPU | 内存 | 存储 | 月流量 / 带宽 | 价格 | 购买 |
| --- | --- | --- | --- | --- | --- | --- |
| ASSD0 | 1核 | 768MB | 10GB NVMe | 500GB / 200Mbps | $13.99/年（活动价） | [查看 ASSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| ASSD1 | 1核 | 1GB | 25GB NVMe | 1TB / 500Mbps | $20.99/年 | [查看 ASSD 套餐](https://www.dmit.io/aff.php?aff=12046) |
| ASSD2 | 2核 | 2GB | 50GB NVMe | 2TB / 500Mbps | $37.49/年 | [查看 ASSD 套餐](https://www.dmit.io/aff.php?aff=12046) |

**日本与保加利亚**

| 套餐 | CPU | 内存 | 存储 | 月流量 / 带宽 | 价格 | 购买 |
| --- | --- | --- | --- | --- | --- | --- |
| JSSD（NTT 线路）入门款 | 1核 | 768MB | 10GB NVMe | 500GB / 200Mbps | 约 $23.39/年（9 折后） | [查看日本套餐](https://www.dmit.io/aff.php?aff=12046) |
| JSSD（NTT 线路）1GB 款 | 1核 | 1GB | 25GB NVMe | 1TB / 500Mbps | 约 $35.99/年 | [查看日本套餐](https://www.dmit.io/aff.php?aff=12046) |
| 日本软银线路入门款 | 1核 | 768MB | 10GB NVMe | 250GB / 30Mbps | 约 $35.99/年 | [查看日本套餐](https://www.dmit.io/aff.php?aff=12046) |
| BGSSD 保加利亚 NVMe | 1核 | 768MB | 10GB NVMe | 按套餐规格 | 按官网标价，可用 10% 折扣码 | [查看保加利亚套餐](https://www.dmit.io/aff.php?aff=12046) |

## 超量之后会发生什么

这一条比价格更容易让人后悔。

DMIT 全线采用「流量跑完不停机、限速继续用」的策略，具体限速值随线路和配置变化：Pro 系列 4~10Mbps，EB 系列 4~8Mbps，T1 系列则宽松得多，100Mbps 到 1Gbps 不等，部分大流量款超量后仍有 500Mbps。对偶发超量的用户来说，这意味着一台机器不会在某天突然断网。

HostDare 这边，官方的规则说明没有这么清楚。V2EX 有用户反馈「流量跑满后机器会被暂停」，也有帖子里提到「收了个 28 刀的直接扔了」。这条我没有在官方文档里找到对应说明，**建议下单前直接在工单里问清楚流量用尽后的具体处理方式**，别默认它和 DMIT 一样限速了事。

## 优惠码：能用，但别当决策依据

两家的优惠码不能通用，各用各家。

HostDare 近期的常规折扣码：

| 优惠码 | 折扣 | 适用范围 | 条件 |
| --- | --- | --- | --- |
| W3VMAXF40N | 10% 循环 | CSSD / CAMD / CKVM | 新订单，年付起，3 个账期 |
| XY604XMHXK | 25% 循环 | 洛杉矶便宜款（SSD / HDD / ASSD） | 新订单，半年付起，3 个账期 |
| WWP2OEG8IM | 10% 循环 | 日本 NKVM / JSSD | 新订单，年付起，3 个账期 |
| QQKF3H319D | 10% 循环 | 保加利亚 VPS | 新订单，年付起，3 个账期 |

HostDare 还有两个常被忽略的隐藏福利：ASSD / SSD / HDD 系列年付后提交工单可申请**双倍内存 + 双倍月流量**；CSSD / CAMD / CKVM 系列年付后可申请**免费把端口升级到 100Mbps**。这相当于把入门款的 30~50Mbps 直接抬到 100Mbps，对回国访问的提升比较直接，年付用户别忘了发工单。

DMIT 的优惠码更新更频繁，多数是按机房、线路和付款周期分别设置的循环折扣，例如洛杉矶 Pro / EB 年付、香港与东京 Pro / T1 季付及以上都有对应的折扣码。它的优惠码属于限时性质，这里就不逐条列了——**下单时在购物车填入优惠码点 Validate Code，能不能生效立刻就知道了。**

## 稳定性与售后

DMIT 近期被反复提到的是攻击问题。香港和东京节点曾遭遇持续 DDoS，官方发布过补偿方案；洛杉矶方面也有用户反馈被打之后流量被清零、IP 被墙，提交工单后获得了换 IP 处理。另外前面提到过，LAX.sPro 高防线已经停产，需要高防的用户得另找方案。

HostDare 在中文社区的口碑两极。有人在 V2EX 专门发帖说「看见 HostDare 赶紧跑」，评论区提到的问题集中在三处：单向 CN2（只优化回程）、特殊时期可能直接拔线、机器一年故障数次。同时也有用户认为它「比较小众反而稳」。这类社区评价本身样本有限，但值得作为风险参考——**如果你是拿它跑生产业务，这些历史反馈需要纳入考虑。**

两家都支持支付宝，DMIT 还支持微信支付和信用卡，HostDare 支持 PayPal。两边都有中文工单。

## 怎么选

**预算敏感、跑轻量站点或做中转**：HostDare 的 ASSD 系列，年付十几到二十几美元，500Mbps 端口，配 25% 循环折扣码。别指望回国速度，它本来就不是干这个的。

**面向国内用户建站、在意晚高峰**：DMIT 洛杉矶 Pro 或 EB 起步。Pro.TINY 月付 $12.98，比 HostDare 的 CAMD1 季付贵不了多少，但换来的是去程也优化的线路和超量不停机的兜底。如果预算卡得死，HostDare CSSD2（年付约 $85.99，用码再打折）是能接受的折中——前提是你接受去程可能拥堵，并且记得去申请免费的 100Mbps 端口升级。

**国际业务、大流量、不在意回国速度**：DMIT T1 系列。T1.WEE 年付 $36.90 就能拿到 4Gbps 端口和 1TB 流量，超量后还有 100Mbps，同价位很难找到带宽更大的；V4C4G 月付 $36.90 给到 20TB 流量，对下载站和备份场景很够用。

**香港或东京节点需求**：DMIT 是这两个区域里线路完成度更高的选项，香港 Pro 走三网 CN2 GIA 回程，但价格是另一个量级——HKG.Pro.TINY 月付 $39.90，跟 HostDare 整个 CN2 GIA 系列的年付价格差不多。香港 T1（$36.90/年）只适合面向海外用户的场景，电信用户买 T1 可能出现延迟比美西还高的情况。

## 常见问题

**CN2 GIA 到底贵在哪？**
它走的是中国电信面向企业客户的精品线路（AS4809），晚高峰不容易被普通宽带流量挤掉。判断一条线路是不是真 GIA，看 traceroute 里有没有 59.43.x.x 的节点。DMIT 洛杉矶 Pro 是三网回程都走 GIA，HostDare 的 CN2 GIA 系列是回程优化、去程走普通直连。

**两家能不能装 Windows？**
HostDare 可以，CSSD 从 CSSD2 起、CAMD 从 CAMD3 起支持，需要自备授权。DMIT 的系统支持范围请以其官网产品页说明为准。

**流量跑满会怎样？**
DMIT 明确是限速不限量，不会停机。HostDare 的规则建议下单前问客服确认。

**优惠码能叠加吗？**
不能，通常以折扣力度更高的那个为准，而且只对原价生效。另外月付一般用不了优惠码，需要年付或更长的付款周期。

如果你想直接看当前库存和实时价格，从下面这个入口进去最快：

👉 [查看 DMIT 全部套餐与当前库存](https://www.dmit.io/aff.php?aff=12046)

最后提醒一句：这两家的库存都很不稳定，DMIT 的热门年付款经常补货即秒，HostDare 的入门款（CSSD0、CAMD0）也长期处于缺货状态。看到有货再犹豫，很可能就没了。
