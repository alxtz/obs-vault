**Memory Synchronization**
- shared lock 另個主要功能，是讓他會被 update lock 所阻擋（主要是為了防範不會讀取到中間值，讀到更新前/更新後其實沒差）
- 另個額外優點是跟 memory register 的拿取/更新有關
---
- 簡單來說，就是你多個 thread/GR 去共同讀一個 global var 時
- 就算 GR A 先執行了，更新了 global var，你後來才跑的 GR B，由於 CPU mem cache 的存在，我們不可能隨時使用變數時，都跑去 real mem 裡面確認/再去拿一次，所以 GR B 就算比較晚執行，他使用到的 var 可能還是舊的
- 所以用 MutEx 和 channel 還有另一個額外優點，他會去告知 CPU 該 flush mem，確保 CPU 的 mem cache 都是 thread safe 的
- ![[Pasted image 20220416052314.png]]