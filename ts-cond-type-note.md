### 一些為了讀書會需要，預先找的內容

- [blogpost] generic type params 的預設值
   - https://mariusschulz.com/blog/generic-parameter-defaults-in-typescript
- 目前讀書會讀的進度
   - https://mariusschulz.com/blog/generic-parameter-defaults-in-typescript
   - 上週的內容 https://hackmd.io/WZWK8ZBzQVS4XOZBkAxk2Q
- conditional type 的本文 https://www.typescriptlang.org/docs/handbook/2/conditional-types.html
   - 不過 generics 就已經帶到一些 cond type 的內容了 https://www.typescriptlang.org/docs/handbook/2/generics.html

---

講 cond type 可以用到的素材

- [How to Write TypeScript Like a Haskeller](https://serokell.io/blog/typescript-for-haskellers)
   - 有帶到 conditional type 跟 HS 會有的寫法不同，跟一些 HS 的 pattern
- [Dependent types in TypeScript?](https://www.hacklewayne.com/dependent-types-in-typescript)
   - 這篇也是 HS 的角度，應該是我關鍵字使用 `typescript conditional types haskell` 的關係，主要好像是講解各種基本型別的特性，從 `dependent type` 出發
- [Type Families in Typescript](https://www.javiercasas.com/articles/typescript-type-families) 跟第一篇有點像，但這篇很詳細的在探討特別是 cond type 與 HS 的 pattern matching，在 type factory 和真正程式上 branching 會怎麼實作，算是最值得拿來當範例的
- https://stackoverflow.com/questions/2973284/type-conditional-controls-in-haskell
   - 單純詢問 HS 如何實作 cond type
- https://en.wikipedia.org/wiki/Algebraic_data_type
   - 我有點不確定 ADT 到底是不是就跟 cond type 有關，ADT 有點像是更廣的定義，那就是 type 可以做代數運算，狹義上代表支援  union，以及做為 `Set` 的集合論特性
   - 但是否跟 cond type 有直接關係還不確定，要看了才知道
- mapped types 也是個大話題，似乎跟最早的 [TypeScript Constraint Types Proposal](https://gist.github.com/dead-claudia/25c0c25b05548a220d1c7e93a3ff35f5) 有關
   - 不同的程式語言，在 generic type 上延伸作 cond/ADT 所提供的工具還滿不一樣的
      - 有時候是 mapped conditional type https://github.com/microsoft/TypeScript/issues/12424
      - 有些人會要求 does not extend https://github.com/microsoft/TypeScript/issues/42177
      - 有些人會要求 nominal type constraints https://github.com/microsoft/TypeScript/issues/202
      - 怪怪的 unit types https://github.com/microsoft/TypeScript/issues/364 (我覺得不太重要，用 lib 就好)
      - 早期的 handbook 有把不少這些核心概念寫下來 https://github.com/microsoft/TypeScript-Handbook/blob/master/pages/Advanced%20Types.md
      - `where` style constriant https://github.com/microsoft/TypeScript/issues/42388

---

追加，有夠難的 https://github.com/type-challenges/type-challenges

我只想看懂 https://github.com/type-challenges/type-challenges/issues?q=label%3A4037+label%3Aanswer

https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/request/index.d.ts