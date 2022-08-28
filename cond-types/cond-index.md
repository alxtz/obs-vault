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

### [Section 1] Conditional Types 介紹  & 使用範例

到這段為止的範例，都只是介紹基礎語法，Section 1 也只是介紹了 overload 語法，轉換成 conditional type 會怎麼寫

![](cond-types/__imgs/cond-index-0827034740.png)

---

### [Section 2] 以「制約」（Constraints）為底的實作方式

這段比較是在說明，generic constriant vs conditional types 的寫法，會帶來的行為/功能/目標上的差異

- generic constriant 比較接近作 validation
- conditional type 則是可以根據給的型別，來實作出任何可能的 mapping 邏輯，回傳正常值，會是 rejected type `never` 其實都可以

![](cond-types/__imgs/cond-index-0827035217.png)

### [Section 2.1] `infer` keyward

雖然他的描述有點像是在介紹 type extraction 的本質，但我覺得 TS 本身型別系統的 conceptual integrity 不太好，有滿多型別工具都是零散的，所以 type extraction 其實不算是一個有被完整支援/是著擴充到整個語言的功能;

我覺得讀這小節主要用來理解 `infer` 這個語法的 meaning，能夠讀懂他是闡述了什麼邏輯就好; 至於裡解這段 code 為什麼要用 infer，infer 代表了何種 pattern，以及 infer 到底好不好，這個問題應該要解釋太久，而且答案也滿難的

![](cond-types/__imgs/cond-index-0828190822.png)

- 裡解 infer 基本上是什麼
   - https://www.youtube.com/watch?v=TGGNWcXx9wQ
- 介紹幾個經典的 type challenge，並用 infer 來實作他
   - https://www.youtube.com/watch?v=3Fxoxg_FMpg
   - 更多 type challenge https://www.youtube.com/watch?v=Ikl_R-BMKCc
- 「比較」實務的用途，用來實作 type factory/generics，作為 type extraction 的用途
   - https://www.youtube.com/watch?v=ObZQM7bx81c

而實際上 2.1 的範例也沒講到太多什麼，只是講 return type 是如何實作的（我覺得是個對 infer 滿差的介紹就是）

[note]
infer 這個型別也實在是十分少見，在以下常用 lib 的型別書寫裡，都沒出現 infer

- `request/index.d.ts` [https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/request/index.d.ts](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/request/index.d.ts "https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/request/index.d.ts")
- `axios/index.d.ts` [https://github.com/axios/axios/blob/v1.x/index.d.ts](https://github.com/axios/axios/blob/v1.x/index.d.ts "https://github.com/axios/axios/blob/v1.x/index.d.ts")
- `express/index.d.ts` [https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express/ts4.0/index.d.ts](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express/ts4.0/index.d.ts "https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express/ts4.0/index.d.ts")
- `moment/index.d.ts` [https://github.com/moment/moment/blob/develop/moment.d.ts](https://github.com/moment/moment/blob/develop/moment.d.ts "https://github.com/moment/moment/blob/develop/moment.d.ts")

### [Section 3] Distributive Conditional Types

![](cond-types/__imgs/cond-index-0829025252.png)

其實我不太習慣 TS 將各個型別功能，都分別取一個 feature 名稱（像是叫這個 Distributive Conditional Types, 以及之前在 FAQ 裡面看到的 homomorphic mapped types）

這其實會讓我有點覺得 TS 不存在一個能統一貫徹他型別系統的設計理念，而只是根據有什麼需求，就發明一個新的 behavior/whac a mole; 這樣對初學者不太友善，也會對這語言的持續發展/pattern develop 有負面影響

但總之，這個 D-C-T 跟 H-M-T 其實問題，或概念是滿像的，consider the following

```typescript
ToArray<string | number>;
```

ToArray<α> 這個型別，會產生一個裡面元素都是 α 的陣列（α[]）;

但由於 α 是一個 union type，你對這個型別所產生的結果，其實字面上可以有兩種解讀

α[] 可以是 `string[] | number[]` 這個型別，只分別允許 string 或 number 做為 child，而不能共存

α[] 也可以被解讀成 `Array<string | number>`，用來表示一個 string, number 能共存的陣列型別

而 TS 預設的行為是解析成前者，將 union type 的各種可能分別做（數學上的）映射，而產生另個 union type

想要後者的 behavior 可以這樣寫

```typescript
type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;
```

---

### [補充] 關於 infer 的設計

[context]

![](cond-types/__imgs/cond-index-0829033723.png)

[infer 幾乎都可以用其他寫法來替代的範例]

https://hackmd.io/7jd5BFiIQGqOEoHl5kobUA

[在其他語言，好用的 pattern matching/type, val extraction 的寫法]

https://elixirschool.com/en/lessons/basics/pattern_matching

```elixir
greeting = "Hello"
# [output] "Hello"

greet = fn
  (^greeting, name) -> "Hi #{name}"
  (greeting, name) -> "#{greeting}, #{name}"
end
# [output] #Function<xxx/2 in :erl_eval.expr/5>

greet.("Hello", "Sean")
# [output] "Hi Sean"

greet.("Mornin'", "Sean")
# [output] "Mornin', Sean"

greeting()
# [output] "Hello"
```

像是這些語言，想寫 swtich-case/取值的操作，function 定義出來就直接包含了實作 & 型別了，所以不存在需要幫他寫這種 `infer U` 行為的需求。（所以這樣 infer 只剩下 type factor 的用途）

[ReasonML]
https://reasonml.github.io/docs/en/pattern-matching

滿多範例不算看得很懂，他 switch 好像不只可以用在 val 上來作為一個 imperative statement，他好像也可以來 switch 一個 function/function call/type，來同時宣告 + 定義行為

[tc39 proposal-pattern-matching]

ECMAScript 也有相關 proposal https://github.com/tc39/proposal-pattern-matching

https://github.com/zhengxiaoyao0716/js-pattern-match

[ts-pattern]

https://dev.to/gvergnaud/bringing-pattern-matching-to-typescript-introducing-ts-pattern-v3-0-o1k

[Intensive reading of "Typescript infer keywords"]

https://segmentfault.com/a/1190000040558014/en

[有用到 infer 的 source code]
https://github.com/DefinitelyTyped/DefinitelyTyped/blob/71881b0d35d22488cea3fa700b6efc2e61267da4/types/react/index.d.ts#L826

---

### 結語