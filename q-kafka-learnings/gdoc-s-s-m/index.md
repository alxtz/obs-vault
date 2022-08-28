
### 回到 `Scalable Service Messaging Resources`

> Current Targets

> Qualitative targets:
> 1. Publicised and encouraged contribution and debate from all relevant stakeholders and captured the highlights in this document. See example here.
> 2. Started and updated implementation plan for Scalable Service Messaging. This will be guided by pilot/experimentals services (probably for automated engineer on-boarding). See plan in this document.

> Quantitative targets:
> 1. Run and documented at least 2 experimental services (probably for automated engineer on-boarding). These experiments will have explored approaches gathered from contributions to this document.
> 2.Benchmarked messages/second of pilot system (System) against baseline metrics.

- 品質目標
  1. 公開/並且鼓勵貢獻和爭執，讓各個相關的 stakeholder 能提供意見，並將這些人的發言收集成精華(highlight)
  2. 啟動，並在必要時更新 Scalable Service Messaging 的計畫，並會由 experiment/pilot services 所指引

> Quantitative targets:
> 1. Run and documented at least 2 experimental services (probably for automated engineer on-boarding). These experiments will have explored approaches gathered from contributions to this document.
> 2. Benchmarked messages/second of pilot system (System) against baseline metrics.

- 數量目標
  1. 執行並記下至少 2 個實驗 service 的狀況，這兩個 exp 該測試/apply 這篇文件上所存在的 feedback/approaches
  2. 要記錄 msg/s of pilot system，記錄下來 metrics

---

### Stakeholders

> Please add stakeholders to this list as our scope grows.
> - Service Development teams
> - Application Development teams
> - Xendit business units relying on Services and Applications built by the above teams.

> More specifically:
> - Engineering Onboarding
> - ISS (Infrastructure and Security Services) Onboarding
> - HR Onboarding
> - Fraud Team (Fraud Case notifications to Xendit’s internal services)

目前會直接用到/被 SSM 所影響的團隊，基本上有

- 會開發各種新服務的團隊
- 會開發各種新 app 的團隊
- 任何會依賴於上列兩者的 xendit 事業群

更詳細地:

- 在 engineer onboarding 時
- 在 ISS onboarding 時
- HR onboard
- fraud team(Fraud Case notifications to Xendit’s internal services) 看起來 fraud team 很像是一個擁有幾乎 total access 的 unit，會綜合分析各 service 的流量，然後發出臨時的提醒來說這可能是 fraud

---

### Open Questions - 問題集

---

### Q1

[question.context]
- [事實] 轉向進入成 event driven architecture 代表著將 "event" 晉升一層，成為各 service 的 first class citizen
  - 但認真來說，目前各 service 都將其視為 2nd class
  - 舉例來說，不是每個 event 都被當作一定必要被 publish 的，並且 event 也不一定有固定存在流程裡的順序，event 也不一定會 snapshot 在 DB 裏（不過 event 也不一定要永久存 db 吧，這樣就變成 event sourcing 了？

[question.question]
- [提問] 需不需要設計一個方式/流程，來將 event 推展成 1st class citizen; 我們有這樣的計畫嗎？為什麼/為什麼不？

![](q-kafka-learnings/gdoc-s-s-m/__imgs/index-0822050752.png)

[case-study]
> One real incident that has happened before by using our current tech stack

> Card <> Fraud system integration, where one of our system accidentally changed without our knowledge that was causing Card system didn’t publish some messages into RabbitMQ. 

> We didn’t realize this issue because it’s not about bad message that was causing server error, but lack of data that was being published. It took almost 1 month for us to identify this issue, and it caused misbehaviour in our fraud detection system, where it works based on data. 

> This condition was affecting our internal metrics and expecting to see some increase of chargeback rate in upcoming months.

範例：其中一個，在使用舊（以往）系統架構時碰到的問題

- 信用卡 <> 和防詐騙系統的串接，其中一個相關的系統，在我們沒被告知的情況下更改了實作。導致信用卡系統並沒有將某些 event 送進 RabbitMQ
- 我們並沒有發現這個 issue; 原因是這不是一個在 MQ 就可以偵測到的錯誤 event。我們幾乎花了一個月才找到這問題。這導致我們的 fraud detection system 在這段時間都 misbehavior，由於他切實的需要準確的資料來運作

```
note: 這感覺比較像是程式寫錯/外部 error 沒 handle 好的問題，要說任何 pattern 能解決他，實在是有點怪; 我也很懷疑是否真的存在那麼死/規定嚴謹的 pattern 能解決此問題

但要說這個真的不會有任何幫助，我覺得也不一定

畢竟在許多 event based 的架構中，雖然定義好了 topic 的型態/行為為何

但對於系統中本身 topic 的 breaking change，或是發送 topic/忘了發送 topic/topic 的 orchestration 出問題的情況，是真的滿沒有規範/工具/特定的模式來知道如何處理, 該處理到何種程度, 預設該如何監控/寫測試的; 所以提出這問題也算是很有幫助
```

- 這狀況觀測上的影響，是在我們 internal biz metric 看到，並且 chargeback rate 發生數個月後才被發現

[stakeholders]
- 擁有者：Fraud(xenshield team)

[stakeholders.1st-priority]
> to have consistency on receiving as same message as the transaction that is being processed by each money-in channel

在每個 txn 被處理時，都該有機制維持/觀測 event 被送出去的 consistency（並且在各個 money-in channel 都要實作

[stakeholders.2nd-priority]
> to receive the message as soon as possible for fraud feature aggregation purpose

msg/event 該能盡早/儘快的被 fraud feature 收存到

[answer]
- SY 想要的解答/工具/手法應該可被統整成以下兩點
   1. 擁有一個夠 robust/抗壓/抗 error 的 msg 系統; 過往其實也有 RabbitMQ 一掛掉，導致 xendit 整體出現 downtime 的狀況
   2. 以例子出發，engineering ops 團隊（這的定位是什麼, 是 half ops, half dev）; ops 團隊可以表列一些 app/service 是遵照「msg 優先」pattern 的; （並且會在 onboarding 指導？ 

---

### Next Steps

---

### 資源表

> Slack Channel: `#scalable-service-messaging`

### Our Current Solutions

---

### rabbitMQ

> rabbitMQ is our primary messaging solution

> Pros (in no particular order)
> 1. AMQP compliant.
> 2. Mature project.

> Cons (in no particular order)
