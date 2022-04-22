- https://stackoverflow.com/questions/29407829/in-jvm-are-thread-objects-tied-directly-to-cpu-cores-or-is-there-a-mapper-in-b
- 所以就算是 OS 等級的 thread，他也只是一種抽象而已，不一定對應到 CPU core
- 所以你在 OS 等級，有 fix mem size 的 thread 上疊床架屋，也不是不行
---
https://stackoverflow.com/questions/304752/how-to-estimate-the-thread-context-switching-overhead
同時 thread 的 context switch 的成本也是很高的，所以你把全部的程式都設計成 thread based 其實也沒多好