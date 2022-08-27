### [介紹] TypeScript - Conditional Types

> 本章內容: https://www.typescriptlang.org/docs/handbook/2/conditional-types.html

---

[大綱]
- 任何程式碼，核心的意義都是描寫 branching 來處理/傳達各式各樣的邏輯
- branching 該怎麼走，都是基於 input，branching 的結果就是 output
- `Conditional Types` & 與此相關的寫法，提供了 TS/其他型別系統，描述此種 branching 方式的機制
   - 常用於
      - 宣告 overload/多型 (input params 不定型) 的 function
      - 描述 function signature, input 跟 output 的對應域邏輯
      - 用來實作進階的型別工具（type factory, generic types, type utils）
- 不同的程式語言/型別系統提供來實作此目的的語法都不同，TS 提供的語法可以說是極為精簡~~（簡陋）~~
   - 正統語法理只有 `?...:` 可以使用 `condition ? trueExpression : falseExpression`，效果等價於三元運算子 or if-else
   - ~~想像一下你寫程式碼只能用 if-else 寫~~
   - ~~同時也只支援 extends 來作為 condition expression，跟 class/interface 的 extend 搞混，有點腦殘~~

---

### Section 1

到這段為止的範例，都只是介紹基礎語法，Section 1 也只是介紹了 overload 語法，轉換成 conditional type 會怎麼寫

![](cond-types/imgs/cond-index-0827034740.png)

---

### Section 2

這段比較是在說明，generic constriant vs conditional types 的寫法，會帶來的行為/功能/目標上的差異

- generic constriant 比較接近作 validation
- conditional type 則是可以根據給的型別，來實作出任何可能的 mapping 邏輯，回傳正常值，會是 rejected type `never` 其實都可以

![](cond-types/imgs/cond-index-0827035217.png)