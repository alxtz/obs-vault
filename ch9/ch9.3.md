**Read/Write MutExes: `sync.RWMutex`**
- 基本上這邊就是額外介紹一個 reader lock，叫做 .RLock() 和 .RUnlock()
- https://www.sqlshack.com/locking-sql-server/
- 跟 **Shared lock (S)** 比較像，DB 裡面是拿來指定某個 select statement 是專門用來提供讀的，並且會被 Update Lock 所卡住
- 值得一提的是
	- 如果以這種 lock 試著實作讀取的 function balance，那是可以的
	- 但如果你是要有類似 checkBalance() 的功能，在出金/付款前使用，你所使用的 lock 就不能使用 shared lock，並且要用 update lock 把所有的讀+寫包住才對
	- 因為這也會導致兩個 GR，在 checkBalance 那層就算會互相 block，他也可能兩個依序跑完，還沒到扣款就通關了
	- 接下來導致扣款這個 action，儘管可能也是包在自己的 update lock 裡面，但他也只是有照順序來，而沒有照該有的 flow 阻擋住第二次的 double spend
	- 所以 shared lock 真的只能用在讀取 val，並且這個 val 沒有提供其他寫入 func 做為參數的時候
```
     func Withdraw(amount int) bool {
		// mu.Lock()
		
	     var isValid = checkBalance() // mu.RLock()
	     
	     if (isValid) {
		     balance = balance - amount // Deposit(-amount)
	     }
	}
```