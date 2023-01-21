休刊 - 第一號

哇，竟然可以持續到第二次出刊

# Work
- 在 exp team sync 時提出了不少對 Nex 2023 後端 initiative 的疑問
   - 後來由 Shun 額外 host 了一個會議來討論 API V4「究竟是什麼」，對於大家的行動力覺得滿開心的
   - 關鍵字：
      - NEX API V4 Pains -> Rough Discussion
      - Nex Go Live - Engineering Preparation -> 我去年真的是不沾鍋，要處理的問題沒半個在我身上
         - 真的要處理的話 Performance Improvement, Testing, Architecture, Others 要挑一下
- 最近我在 Nex Team 的活躍程度也增加了不少，隨著 2023 年的上學/讀書/工作計畫逐漸在發想中，現在也經常有更多的精力能去發表一些長 thread
   - ![](shu-can/__imgs/ep-1-0121143744.png)
      - 想參考 babel https://github.com/babel/babel/projects/13?query=is%3Aopen+sort%3Aupdated-desc 來規劃我們的 technical tickets
   - plus, 寫長文用 ChatGPT 精修真的好有用
- 工作時希望避免的名詞 https://www.grammarly.com/blog/words-to-avoid-at-work/
   - **“Let me know”**
      - It’s easy to tag this catchall phrase onto the end of your emails, but it doesn’t translate into action. If you want results, take some initiative.
   -  **“Just”**
      - You may be using “just” to soften the tone of your message, but it could communicate that what you’re saying isn’t especially important.
        Removing “just” from your vocabulary conveys confidence in your actions. If you know what you need, go ahead and ask for it.
        - [bad] “Sorry to bother you, I just want to check in on your progress on the report due tomorrow.”
        - [good] “How’s progress on the report coming along? I look forward to reviewing it tomorrow.”
     - **“I think” / “I feel”**
        - It’s time to let the security blanket go. These [qualifying phrases](https://www.grammarly.com/blog/qualifiers/) may feel safe, but they’re undermining your credibility. Constantly using “I think” communicates a lack of confidence in your ideas and abilities. Instead, ditch the qualifiers and make assertive statements like:
     - **“I’ll try”**
        - So, will you do what I requested or not? Instead of using this wishy-washy phrase, boost your coworkers’ confidence in you by clearly setting expectations.
          “I won’t be able to write the report today, but I can have it to you by noon tomorrow.”
          “I’ll research how to do that and will check in with Kevin if I need assistance.”
      - **“Sorry”**
         - By all means, apologize when you’re accepting responsibility for an error you’ve made. But let’s take a moment to think about how woefully overused this little word is.
- SQL UNION
   - https://www.foxhound.systems/blog/sql-performance-with-union/
   - https://www.reddit.com/r/SQL/comments/4q4pz3/union_union_all_intersect_except/
   - https://news.ycombinator.com/item?id=26524776
- 認真為 API V4 開始進行 alpha PR
   - ![](shu-can/__imgs/ep-1-0121151659.png)

# Life
- GraphQL: The Documentary
   - https://www.youtube.com/watch?v=783ccP__No8
   - 最近對 GraphQL 這個話題滿感興趣的，部分是因為在 BE sync GraphQL 被當成一個專案的引爆點，另一點是他所實作需要的成本/對 FE 的益處/如何在 graph 的格式下 remodel 東西都讓我覺得很吸引人
   - 有聽 EricL 分享在 Rivian 使用 GraphQL 的經驗，以下
      - 他們的 GraphQL schema 非常大，並且資料可能是從一台/多台 server/SQL/non-SQL DB 來的
      - 聽起來 dataloader 就不好寫，並且有關聯 condition 的 query 產生的 N+1 問題超真實
      - 回答了之前的疑問：使用 GraphQL 後就不太會混 REST API（可能也是因為 GraphQL 自己有一套 caching 機制）所以也不太會出現 migrate 過程中只把 GET API 用 GraphQL 取代。而 POST 還是用舊的的情況
- 藤井風真可愛
   - https://www.youtube.com/watch?v=eGChSavdVc0
- 什麼是 first level cache 和 second level cache https://www.java67.com/2017/10/difference-between-first-level-and-second-level-cache-in-Hibernate.html
- [經典回顧]
   - The Many Meanings of Event-Driven Architecture • Martin Fowler • GOTO 2017
      - https://www.youtube.com/watch?v=STKCRSUsyP0
   - HackMD 招募全端工程師
      - https://hackmd.io/@hackmd/2023-HR-FE-JD
- [上學去]
   - https://www.edx.org/course/advanced-big-data-systems 北京清大也有分散式系統的課，看起來滿讚的
      - 後來看了一下，影片有點廢
   - CSDIY https://github.com/pkuflyingpig/cs-self-learning/
      - https://csdiy.wiki/%E5%B9%B6%E8%A1%8C%E4%B8%8E%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/CS149/ CMU 的好讚
      - https://csdiy.wiki/%E5%B9%B6%E8%A1%8C%E4%B8%8E%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/MIT6.824/ MIT 的比較硬
   - 台大 111學年第2學期隨班附讀學分班第一期
      - https://www.ntuspecs.ntu.edu.tw/specs/tc/classCreditListContent.aspx?id=1838&chk=54b2a7f3-535c-45b7-8313-39025e46e08e&cid=35
      - 台大開的課程有夠爛...
   - 課本 https://www.amazon.com/Distributed-Systems-Principles-Andrew-Tanenbaum/dp/153028175X
   - MIT 課 https://www.youtube.com/watch?v=cQP8WApzIQQ&list=PL4YhK0pT0ZhXTRSAYHAgBcJkhlM2hlhw3
- George Hotz | Programming | what is programming? (noob lessons!) | Science & Technology
   - https://www.youtube.com/watch?v=N2bXEUSAiTI
   - 滿多是 Geohotz 在講一些人生/寫程式的 hot take (也有不少 shit take, 畢竟你想從個 no-life 的人得到什麼人生建議呢)