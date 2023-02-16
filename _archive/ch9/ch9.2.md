**Mutual Exclusion: `sync.Mutex`**
> ![[Pasted image 20220416043432.png]]
---
- binary semaphore
	- 有點像是前一章所實作的 exec token，要能取得這個只有 ch length 1 的 token 才能去執行任務
- 這個 pattern 其實可以直接被叫做 MutEx，基本上就是有一個核心的 .Lock() 和 .Unlock 可以 call
	- \[note\] DB的 lock type 其實會有更多種 https://www.sqlshack.com/locking-sql-server/
---
- 會使用 defer statement 來 unlock，比較乾淨/防呆，並且還可以防止 panic 時 dead lock
---
- Withdraw (先減 balance 再退錢) 的範例有嚴重的問題
	- 首先他不 atomic，他會把改錢的中間值 expose 出來
	- （也會導致 lock 根本無從 apply
	- 並且他天生就有問題，理論上來說 fintech 的這種 function，應該是要先寫個 check balance，直接跑就好
- 解法：使用 lock 兩次不會有效，會直接把 program 卡死
- ![[Pasted image 20220416050402.png]]
- 基本上意思就是，當然你有機會可以把 MutEx lock 實作成巢狀的格式。但這樣還是破壞了 parent 層變數希望維持的不變性，其實反而連原本要解決的問題都放掉了
- 在 code level，要區分有 lock 版本，跟沒有 lock 版本的 func，通常也可以用大小寫 exported 來區分（也可以都是 private 的，改用命名）