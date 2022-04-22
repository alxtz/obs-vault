**Concurrent non-blocking cache**
- not well addressed by existing libs -> 寫的滿嚇人的
- https://github.com/ardanlabs/gotraining/blob/master/topics/go/concurrency/channels/README.md
- 讀到有點無聊，但總之情境跟 withdraw transaction 其實不太一樣
	- withdraw 需要在一開始 check balances 就開始 ex lock，所以後續的 request 自己依照商業邏輯排程/failed 即可
	- 但 cache 又不太一樣，他是讀跟寫在同一個 process 裡面，並且依據情境決定要不要觸發寫入行為
	- 