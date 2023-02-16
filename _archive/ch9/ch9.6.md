**The Race Detector**
- The go memory model
	- https://go.dev/ref/mem
	- 只找到一篇部落格文章，但其實內容還比 TGPL 簡略？
	- 不過其實這段要講的東西跟實作細節也沒有完全直接相關
	- ![](_archive/ch9/__imgs/ch9.6-0423043558.png)
	- 儘管文章裡這樣說，我看實際的專案似乎還是不會去在 prod 使用 `go run/build -race`