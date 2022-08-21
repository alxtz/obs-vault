
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

![](q-kafka-learnings/gdoc-s-s-m/imgs/index-0822050752.png)

[case-study]
> One real incident that has happened before by using our current tech stack

> Card <> Fraud system integration, where one of our system accidentally changed without our knowledge that was causing Card system didn’t publish some messages into RabbitMQ. 

> We didn’t realize this issue because it’s not about bad message that was causing server error, but lack of data that was being published. It took almost 1 month for us to identify this issue, and it caused misbehaviour in our fraud detection system, where it works based on data. 

> This condition was affecting our internal metrics and expecting to see some increase of chargeback rate in upcoming months.

範例：其中一個，在使用舊（以往）系統架構時碰到的問題

- 信用卡 <> 和防詐騙系統的串接，其中一個相關的系統，在我們沒被告知的情況下更改了實作。導致信用卡系統並沒有將某些 event 送進 RabbitMQ
- 我們並沒有發現這個 issue; 原因是這不是一個在 MQ 就可以偵測到的錯誤 event。我們幾乎花了一個月才找到這問題。這導致我們的 fraud detection system 在這段時間都 misbehavior，由於他切實的需要準確的資料來運作

```
note: 這感覺比較像是程式寫錯/外部 error 沒 handle 好的問題，要說任何 pattern 能解決他，實在是有點怪
```


---