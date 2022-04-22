**Race Conditions**
1. 只有單一個 GR 的 program 我們可以稱他為 sequential program
2. 我們也能很自然的讀出/認定他執行的順序 (imperative)
3. 但當有 2 個以上的 GR 同時執行時，就會很難去判斷兩個 GR 裏，不同的 event interleave 的情況
	1. 甚至在 sharing var 的狀態下，就算你是後面執行，可能有些 var 還是 cache 到最早的狀態

---

A type is concurrency safe if all its accessible methods and operations are concurrency safe.
- 我覺得可以理解為，如果一個 module 所使用的所有 syntax/method 都是 concurrency safe 的，那這個 module type 也會是 concurrency safe

---

- **confining**
	- we avoid concurrent access to most variables either by confining them to a single goroutine
	- 搭配閱讀 https://medium.com/@eric.g.yuan/go-concurrency-patterns-%E8%AE%80%E6%9B%B8%E5%BF%83%E5%BE%97-confinement-177537b9213b
	- 基本上就是使用 channel，把所有會用到的 var 都作為 channel 裡的值來分享
- **mutual exclusion**
	- or by maintaining a higher-level invariant of mutual exclusion
	- ![[Pasted image 20220416033416.png]]
	- https://www.youtube.com/watch?v=MqnpIwN7dz0
	- https://www.youtube.com/watch?v=aSYqgHD7FIk
	- MutEx 的幾部介紹影片

---
- exported package level function are generally expected to be concurrency safe
---
- 第一個解法基本上有夠廢，就是說你的 program 不要有 write statement 就好啦（傻眼
---
- 第二種解法，讓寫的行為基本上只去讓一個 channel 執行
	- 前幾章使用 channel 的範例其實跟這個很類似
- "Do not communicate by sharing memory, share memory(data/updates) by communicating"

- 這個其實跟 fintech 領域裡面要解決 serialization 的問題也滿相近的
	- 要解決這種出金/入金的 transaction 要能被 serialize 的問題，一種解法是就是 locking
		- https://www.postgresql.org/docs/current/explicit-locking.html
		- 而 DB lock 會比較接近 MutEx 一點，是用會互斥的鎖，來讓各支程式自動在 critical region 被 block
	- 另一種解法則可以是開一個 message queue/broker, 創建一個 queue/event type 是專門一次只能 process 一個 item 的，並依賴於他來 serialize 操作
		- https://github.com/timgit/pg-boss/blob/master/docs/readme.md#sendname-data-options
		- ![[Pasted image 20220416041619.png]]
		- 像是 PgBoss 就會有這個功能，其他 msg broker 應該也會有
		- 這個主要的差別/不方便性可能會來自於，大部分 web server 使用 broker 的介面都不會直接去等 event received/done，而是拆成好多個 step/listener/retry module(good/bad it depends)
			- 而不會像是 GR/channel 一樣預設就給你 blocking behavior
			- 並且他還是沒有在 DB level 做 locking，如果有多個 service share DB 可能會是問題
---
execToken = make(chan struct{}, 1)
lockedCh = make()