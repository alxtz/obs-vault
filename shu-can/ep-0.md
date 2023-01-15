這集是「休刊」第零號

基本上這個 publish，目前的目標應該只會是紀錄我每週生活 + 工作的 update log

(畢竟我過往實在棄坑太多次，目標先定的小一點)

並且接下來 2023 其實想訂些比較有意思的目標，所以就順著寫(也許台大/台科大修課有上，會更有意思)

言盡於此，就開始吧

# Work
- 這周收尾了某優惠點數的 API 與 balance 系統開發，幾個亮點
   - 我有幾位年資看起來 < 5 的新加坡同仁，在討論熱度/淡季的互動性都很好，相較於台灣人同事就比較「聰明」，大體上在沒事時就會待著休息（這邊不討論好壞
- 在 PR review 中，我現在看到了 `Nullish coalescing operator (??)` 所存在的較多應用，在幾年前 ECMAScript 更新時，我認為以下幾點讓他比較沒被廣泛代替 `||`
   - 簡便性並沒提升太多，並且 polyfill 又可能有一定的風險
   - 在使用 TS 之類的 typecheck 後，單純因為忽略空字串或其他 falsy value 的可能性變很低
   - 現在看到他開始被應用，正向看待，不過不覺得影響有太重大
- 公司開始了以 domain 作為菜刀，module-style 的 monorepo 開發
   - 初次使用跟參與 northstar, 最後成為數個風格的起草者感覺是還不錯的，不過目前的程度比較像較好的 namespace 而已。並沒有延伸到 monorepo build process
   - ![](shu-can/__imgs/ep-0-0116022032.png)
- 在跟同事討論到，現有 spec 沒有數值是 decimal 的可能的欄位，是否該使用 int 來實作有些意見分歧
   - 個人自己在開發上，跟金錢屬性很接近的 prop，基本上都預設會用 IEEE754 safe 的資料型態，並且多數情況都直接預設可能是 decimal（因為我預期未來幾乎都會有小數點商業邏輯的可能）
   - 並且現代這些資料型態的成本都很低，遇到了 perf 問題再來改，也才能知道該往哪個方向走（畢竟又不是一上線就要被 C10K 壓測）
```typescript
@Entity('points_ledger')
export class PointsLedgerEntity {
   @Column({
      name: 'amount',
      type: 'decimal',
      precision: 20,
      scale: 8,
      nullable: false,
      transformer: new DecimalTransformer(),
   })
   public amount!: Decimal
}
```
- 同事提到了在 monorepo/modulize 的結構下，是否也該有 scoped DI 的存在
   - 我還沒找到很好的解法，但 gut feeling 是不太需要，這樣短期能節省的程式碼行數其實滿少的
   - 並且誰也不太能確保未來 module 的 subdomain 會不會 dependency 交互變得複雜
   - 在並沒有高強度 DDD 的場景裡，通常切 domain 的方式可能也不會那麼乾淨，所以我認為保留些彈性是好的
  - ![](shu-can/__imgs/ep-0-0116023219.png)
- 同時實作了一個算是較難 debug 的功能（要有個 ledger 紀錄每筆帳進出，但同時算 balance 為了效能，需要有個 cache column）
   - 這種時候不使用 DB transaction 來實作，就會天生的遇到 concurrency control 的問題
   - （應該有篇 intro 的文章寫的滿好的，但暫時找不到）
   - TLDR: 使用了 Postgres 最高層級的 SERIALIZABLE（底層是 SSI）來同時進行寫入 ledger 跟更新 balance; 因為 SSI 在查到的數據顯示效能已經很好（接近樂觀鎖）了
   - 幾個公司內討論的 snapshot
      - ![](shu-can/__imgs/ep-0-0116024743.png)
      - ![](shu-can/__imgs/ep-0-0116024804.png)
最後實作的 code 大約是長這樣，我認為設計上比較大的問題是 `getOrCreateBalance()` 在 query 時也會用到，這讓我得斟酌在 query api 是否也該使用 `REPEATABLE READ` 或最高層級（因為背後都是 SSI, 但由於效能看起來是很好的，就都選擇最高層級了）
```typescript
try {
   await tx.start('SERIALIZABLE')

   const grantRecord = new PointsLedger({
   })
   
   await this.pointsLedgerRepository.save(grantRecord)
  
   const existingBalance = await this.pointsBalanceRepository.getOrCreateBalance(
      command.userId,
      tx.getExecutor() as EntityManager
   )

  existingBalance.setAvailableBalance(existingBalance.availableBalance.sub(command.amount))

  await this.pointsLedgerRepository.save(grantRecord, tx.getExecutor())

  await this.pointsBalanceRepository.save(existingBalance, tx.getExecutor())
  
  await tx.end()
} catch (error) {
   await tx.rollback()
}
```
- 學到了一個新語法, `expect.assetions()` ![](shu-can/__imgs/ep-0-0116025626.png)
- 同時為了 SSI，實作了一個測試來驗證他的行為
```typescript
it('calcInitialBalance() should behave correctly in concurrency contexts', async () => {
 const MOCK_USER_ID = faker.datatype.uuid()

 const mockUser = UserFactory.build({ id: MOCK_USER_ID })
 await userRepository.save(mockUser)

 const grantledger = PointsLedgerFactory.build({
   userId: MOCK_USER_ID,
   direction: PointsLedgerDirection.ADDITION,
   amount: new Decimal(100),
 })

 const withdrawledger = PointsLedgerFactory.build({
   userId: MOCK_USER_ID,
   direction: PointsLedgerDirection.DEDUCTION,
   amount: new Decimal(80),
 })

 const testTx1 = connection.createQueryRunner()
 const testTx2 = connection.createQueryRunner()

 const func1 = async () => {
   try {
     await testTx1.connect()
     await testTx1.startTransaction('SERIALIZABLE')

     const executor = testTx1.manager

     await pointsLedgerRepository.save(grantledger, executor)

     const result = await pointsLedgerRepository.calcInitialBalance(MOCK_USER_ID, executor)

     await testTx1.commitTransaction()
     await testTx1.release()

     console.log('result1', result)

     expect(result.toNumber()).toBe(100)
   } catch (error) {
     console.log('error1', error)
     await testTx1.rollbackTransaction()
     await testTx1.release()
   }
 }

 const func2 = async () => {
   try {
     await testTx2.connect()
     await testTx2.startTransaction('SERIALIZABLE')

     const executor = testTx2.manager

     await pointsLedgerRepository.save(withdrawledger, executor)

     await setPromiseTimeout(1 * 1000)

     const result = await pointsLedgerRepository.calcInitialBalance(MOCK_USER_ID, executor)

     await testTx2.commitTransaction()
     await testTx2.release()

     console.log('result2', result)

     expect(result.toNumber()).toBe(-80)
   } catch (error) {
     /* should throw: DomainError: could not serialize access due to read/write dependencies among transactions */
     /* explain: txn 2 had significant longer run time that interleaves with the records txn 1 uses, in SSI, the txn that gets committed later would be rejected */
     console.log('error2', error)
     await testTx2.rollbackTransaction()
     await testTx2.release()
   }
 }

 await Promise.all([func1(), func2()])
})
```
- 公司內部在討論 API V4, 並且提出該不該加 GraphQL 進來
   - V4 的定義很模糊，想解決的問題也很模糊，總感覺這討論從頭就怪怪的
   - GraphQL 算是另個 initiative，做出一個實驗性 endpoint 應該滿簡單，但花力氣去設定 dataloader, 把常見的 N+1/其他問題全解決，並且定義一個 mid layer 似乎有點花太多工了
   - 還是可以做做看，畢竟沒真的玩過

# Life
[2023-大學修課]
- 看起來台大課程網站已經公布 2023 下學期會有的課了，有稍微瞄過
   - https://nol.ntu.edu.tw/nol/coursesearch/search_for_02_dpt.php?current_sem=111-2&coursename=&dptname=9020&dpt_sel=9000&yearcode=0&teachername=&selcode=-1&alltime=yes&week1=&week2=&week3=&week4=&week5=&week6=&allproced=yes&proced0=&proced1=&proced2=&proced3=&proced4=&procedE=&proced5=&proced6=&proced7=&proced8=&proced9=&procedA=&procedB=&procedC=&procedD=&allsel=yes&selCode1=&selCode2=&selCode3=&page_cnt=100
   - 有趣的是，資料結構跟演算法是開在一起的，並且沒有分散式系統/DB 的課
   - https://www.ntuspecs.ntu.edu.tw/specs/tc/classCreditListContent.aspx?id=1810&chk=e268eed9-b7aa-4c85-87f4-aacd17880e17&cid=35
   - 隨班附讀章程會在 1 月第 3 周出來
      - 等的過程我覺得很像是場卡夫卡式的夢魘，同時讓我思考該不該買卡夫卡的「審判」來看
      - https://tw.voicetube.com/videos/39639

[random]
- 最近外出工作的需求增加，正在試著把 remote desktop 環境弄好（聽說用 screen share app 就能做到，但 MacOS 的更新讓我還沒能試過
![](shu-can/__imgs/ep-0-0116030452.png)
- 對於 crypto 2022 很好笑的 depict https://twitter.com/cmsintern/status/1605604428194598915
- 開始使用了 delta 作為我的 git.pager https://dandavison.github.io/delta/installation.html
![](shu-can/__imgs/ep-0-0116030652.png)
- second dinner 在徵才，不過後端似乎是寫 .NET https://seconddinner.com/work-together-at-second-dinner/
- 我非常喜歡的 subreddit https://www.reddit.com/r/ExperiencedDevs/
  最近似乎開始有些抱怨 https://www.reddit.com/r/ExperiencedDevs/comments/10b4x3e/is_this_place_becoming_a_rcscareerquestions/
   - 因為新進的工程師太多，導致太多人在問薪水
   - 對於職涯/寫程式的看法變得十分功利（像是有人因為沒升遷加薪到，就開始抱怨說失去 motiv，這種問題多到爛大街了
   - 對於開發/使用工具的看法越來越廣 big-corp 靠攏，沒有什麼品味跟 art 存在