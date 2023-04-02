目標：符合目前 Nex team member 的程度（L4-L5, intermediate BE eng）

重點並不一定是在於完全實作，而是了解不同種類的 service/概念

可以引申到 event-driven arch 最好

---

閱讀進度：

緊繃：一週念 15-20p

適當/寬鬆：雙週制度，一次 30p

450p 的書 -> 

450/30 -> 15 個雙週 -> 7.5 月（上限）

---

書籍選擇：

- microservices patterns
   - with examples in Java（難怪這麼多 DDD）
   - [x] 會不會太多 Java example, 導致看的有點分心（需要驗證）-> 會
   - [x] 確認一下會不會太冗 -> 會
   - 幾乎能學的都學到了 -> 你會變成一本字典
   - 460p -> 7.5 個月，會讀到沒耐心
   - 章節
      - (1) 介紹/定義（monolith）, 單體式架構的特性
      - microservices 是什麼，模組化是什麼
      - 各個 service 該不該有自己的 DB
      - microservices 的優缺點
      - microservices 有哪些術語（domain language）
      - **(2)** 關於組織與 delivery
      - 詳細定義 what is a microservice architecture
      - 如何去做 decompose
         - **試著定義 + 論證太多概念和新名詞了**
         - **讀完可能可以成為 microservices + DDD 的字典，但對 L4 來說會過於抽象/非質化概念太多**
      - (3) microservices 間如何互動
      - API/msg 做為溝通模式
      - 指令溝通方式（REST/gRPC）
      - 非同步 messaging（定義 interaction style, 使用 message broker, transactional messaging）
      - 去除同步形式的溝通
      - (4) distributed transactions（sagas），實際實作 sagas
      - (5) 套用 DDD, model 商業邏輯, event & domain events
      - **(6)** event sourcing -> 解決還是會存在的 transactional persistance
      - optimistic locking/snapshots/idempotency/drawbacks of event sourcing
      - event sourcing sagas
         - 問題定義的很詳細/解釋 edge/pros/cons/各種選項都很好
         - 但還是太冗了，沒必要花那麼多篇幅說明 java 裡用哪個 lib 能實作 saga, 以及 ORM mismatch 都講？？
      - **(7)** API composition pattern（聽不懂是啥）, CQRS 詳解, + DynamoDB（???
      - (8) API client, API gateway, GraphQL
         - 講解 CQRS + storage engine integration 寫的還行，pros/cons 算詳
      - **(9)** testing（測試 MS 的挑戰），entity/sagas/doamin/event 的測試（unit
      - **(10)** integration tests（pub/sub, contract tests, e2e tests）
      - (11) tranditional monolith 的部署/資安, push/pull based, health check/logs/service mesh
      - (12) 部署, VM, docker, kubernetes, zero downtime, serverless
      - (13) 如何重構 MS

---

- building envolutionary architecture
   - 160p -> 2.5 個月
   - 讀起來也有點像是 "building microservices", 滿多引用跟 essay 類的寫法，但也都離實作有點偏遠
   - 章節
      - **(1)** 演進式的架構, 如果東西經常改變, 那怎麼有可能進行任何長期規劃
      - incremental change, conway's law
         - 讀起來太像是 essay 了，很多快要變成解決組織等級的問題
      - (2) fitness functions (看不懂)
      - **(3)** engineering incremental change
         - 主要都跟 fitness function 和其他公司的方法論有關
      - 測試/fitness function, hypothesis & data-driven development
      - **(4)** architechtural coupling, granularity, monolith, event-driven architectures, serverless
      - **(5)** 可演化的 data, 可演化的 DB design, 可演化的 schema, 2-phase commit transactions
      - (6) 建構可演化的 architectures, identify dimensions, define fitness functions, deployment pipelines
      - **(7)** evolutionary architecture 的 pitfalls & anti-patterns
      - (8) putting evolutionaary architecture into practice, team, cost

---

- building microservices
   - 250p -> 4 個月
   - 光看 abstract 的話，能得到的資訊不多;
      - 感覺是把 460p 那本只留下純理論性/知識型的節點
      - 缺點可能是太概念性，或看的角度太高（有點 martin fowler 說教感
   - 感想
      - 這本書更像是好幾篇分散式系統 + 初期 microservice 概念，有關 essay 的縫合怪
      - 如果當作理論性的入門還算可以讀（甚至滿不錯的），但如果有實用性的需求（or 想結合工作），沒那麼適合
      - 如果真的比較，以 Nex team 的需求來看，其實 Java 那本可能還更好
   - 章節
      - **(1)** 定義/scalability/組合性
         - 第一章什麼都沒講，只提到一些歷史/拆分啥的
      - (2) 架構師的角度：監控/定義 interface/governance
      - **(3)** 如何 model：高耦合低內聚/如何拆分
         - 這本書好像是著重在如何從 monolith -> microservice，而且是 microservice 剛出來大家要學的那時代
      - **(4)** shared DB, sync vs async, RPC, local vs. remote call, REST vs RPC
         - 講太多關於 exchange format 的論證跟比較了
         - L4 不太會派他去自己單獨開一個 service，決定要用哪種 format, 跟處理 event versioing/migration 的作業
         - 所以反而太高層級的東西，也不容易幫助每個人做事
         - 最後討論到第三方還不錯，但也沒介紹太多
      - (5) （實作上）如何開始拆分
      - (6) 部屬 CI/CD，docker
      - (7) 測試/unit/service/e2e
      - **(10)** conway's law
         - 只講了 conway's law (lol)
      - **(11)** timeout/circuit breakers/load balancing/CQRS/caching/CAP
         - 只提到概念, 沒引申
         - 讀起來有點像好幾篇 essay 的縫合怪