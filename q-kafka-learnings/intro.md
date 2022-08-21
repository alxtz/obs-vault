# 為什麼會從 RabbitMQ 轉到 Kafka

---

這個 init 起源於現在 BE nex-api 本身已經從純 aggregator，轉換到需要成為一個 general system，雖然還沒到需要拆成 MS 這階段，但已經是有一定規模的系統了，都會需要某種程度的 message queue，或是其實也存在著不使用 msg queue 的 event driven system（要歸類一下差別）但以下是 X 公司，從原本就有 MSG queue，使用 RabbitMQ 的狀態，卻又要轉到卡夫卡，旁人的觀察集

---

通常系統提案，試著提出改進不會只有一個討論/協作，主要的提案通常是 RFC，以下是幾個相關的討論文件

- https://docs.google.com/document/d/1eGRQbHm9NGKWTUFNNLHa0TtTSAKb8uvYJOpifopVkzc/edit#heading=h.g8xp4dd3f42l 這份是舊版的整體討論文件

---

### [google docs][Scalable Service Messaging Resources]

- https://docs.google.com/document/d/1eGRQbHm9NGKWTUFNNLHa0TtTSAKb8uvYJOpifopVkzc/edit#

> Messaging or event-driven architectures can improve scalability and simplify communication between services / applications.

算是吹一下 event driven arch 的優點，基本上可以在溝通端良好的定義/scale service/app 之間的互動

---

> Current Targets
Initiative started: 2021-01-13. See this slide deck for background. See also this metric tracker.
Targets to be met by 2021 Q1 (31 March 2021).

背景原因可以被這篇 slide 帶到

![](https://i.imgur.com/Gn6Izgb.png)

@SiuYin 提到

- 可擴充的 msg system 必須要能達到以下目標
  1. 提供 service, publish 和 subscribe 的介面，可以視為 service 們使用的 slack channel
    - slack channel 這個範例算是滿生動的，不過之前在 bob(?) 的演講裡其實有提到，雖然是 event based 的 arch，但使用 command vs event 這兩種看法，其實對後續 DDD 的演進會有滿大差別
  2. `Improve scalability. Requests are “accepted” immediately and followed-up later. We already do this with rabbitMQ.` 使用 msg queue 上述的優點是可以直接因為拆成 async 而提升 scalability，request 被送出後會直接被 accept，而不同層所需要的效能/機器會比較容易擴充（缺點是會有更多 point of failure, branching 存在）（以及 msg queue 本身會不會直接 accept request，我看 RabbitMQ 和 Kafka 也有不少 pattern 上的差別？
  3. `Detect Remote Service Downtime and enable actions on it. See “Opportunities” in the doc below.` 這個沒有很看得懂，原串比較像是某時候 event 突然全部都 stuck，我猜只是在描述，在 event driven arch 底下，雖然 point of failure 更多，但也更容易透過監聽/自己 sub 來疊架觀測的架構，等到 Oppo 再詳細看，同時又 loop 到原本的 google doc

---

### 2021 Engineering Metric & Targets

|     | Messages / second | Cutomers care that their messages and events are delivered in a timely fashion. | Number of messages of size 1kb published per second and 99-th percentile latency of message delivery. | Siu Yin plans to develop existing Engineering Ops Team to develop a test-bed/experiment for this Initiative. | See rabbitMQ / Metrics in [doc](https://docs.google.com/document/d/1eGRQbHm9NGKWTUFNNLHa0TtTSAKb8uvYJOpifopVkzc/edit#). Max about 400 msgs/sec with no metrics on p99 latency. | 1.2x baseline performance | 1.5x baseline performance | 1.8x baseline performance | 2x baseline performance |     | No Data | 168.6975 | Same as March,No Update | No update | Working group decided to try out kafka as a managed service. | ISS did an initial deployment and tear-down with AWS' Managed Service for Kafka. | NGS / HA NATS suffered intermittent disconnects. | NATS Single Stable on Staging and Production | Kafka (MSK) available in Staging and Production. | [RFC Pilot](https://docs.google.com/document/d/1hfo-eyc2Eg8isot_wp7FMHaIpHTktwjPR6BtOs6ppR0/edit#heading=h.8plvudtd3xh6) launched with 2 projects in Development and 2 more in planning. |     |     |     |     |     |     |     |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|

![](https://i.imgur.com/2wDgmFc.png)

![](https://i.imgur.com/sGCcn4f.png)

![](https://i.imgur.com/xL4bvOc.png)


- [使用者在乎][每秒多少訊息]
  - [why] 使用者們很在乎他們的 msg/events/webhook 會盡快的送達
  - [how to measure] 小型(1kb) 的 msg 每秒發送多少，並且 PR99 的 msg deliver latency 是多少
  - [needed] siu yin 會想和其他人協作，產出一個 test-bed/experiment 來啟動這個
  - [baseline(metric)(6 month moving average)] 同時要參照 rabbitMQ metrics, 最高是 400msg/s，PR99 latency 目前沒觀測（我也想這個怎麼監控，畢竟沒好好定義 latency 從哪階段開始定義，所以以前可能也是沒 track 這段的）
    - Q1 ~ Q4 target 不知道為何都是直接定義成倍數成長;
    - ![](https://i.imgur.com/4dAthke.png) 後面好像就有開始 track 了，但描述的也不完全是 metrics，有點怪
- [使用者在乎][多少內/外部系統使用 scalable service messaging]
  - [why] 使用者們會希望他們的 msg 和 events 都是帶來正面價值的，有價值的 xendit system 在使用這個 infra，也能代表這個 infar 的價值
  - [how to measure] 總共使用我們這個 infra system 的 service
  - [needed] 同上
  - [baseline] 確定目前 xendit 有多少 pub/sub 系統在 run，AWS SNS SQS(??)，new webhook service，iluma kafka pub/sub(??)
    - 反正接下來就是要盡可能支援更多的 service

---