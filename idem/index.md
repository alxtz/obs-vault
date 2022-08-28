https://medusajs.com/blog/idempotency-nodejs-express-open-source/
> ## Introduction
> This post covers how idempotency keys are used in [Medusa](https://www.medusajs.com/) and how you can implement your own idempotency key logic in a NodeJS application to make your API more robust. This post and the implementation discussed here are inspired by [this article](https://brandur.org/idempotency-keys) by Brandur.

總之 Medusa 看起來是一個 open source headless commerce store, 比肩 shopify
![](idem/__imgs/index-0616144845.png)

不過 shopify 其實有滿多金流，實際的 integration 跟需要開發人力的東西（也許還有 BD），真的找得出 OS 的替代嗎

---

被 https://brandur.org/idempotency-keys 這篇所啟發的，所以 Medusa 這篇說不定比較淺

---

> Idempotence is a mathematical term used to describe algebraic expressions that remain invariant when raised to a natural power

> refers to the idea that you can perform an operation multiple times without triggering any side effects more than once

數學上的 idempotency (冪等性)，基本上最容易知道的就是 abs (取絕對值)

---

> extremely powerful property for fault tolerance in larger systems where service availability cannot be guaranteed

> If you are familiar with RESTful design you have probably heard that `DELETE` requests should be idempotent, meaning that no matter how many times you make a `DELETE` request on a certain resource it should always respond with confirmation that the resource has been deleted (unless business rules don't allow it that is).

像是 REST 裡面，DELETE 會實作成永遠都回傳 204, 不管有沒有存在過

就算不存在，也是 204

> In fintech applications, idempotency is typically extended to other types of requests to ensure that sensitive operations like issuing money transfers, etc. don't erroneously get duplicated

> Stripe has support for idempotency on all of their requests controlled by an `Idempotency-Key` header.

> ; however, the more systems that are connected the more prone you will be to having inconsistencies across your tools,

不過 idempotency 在 FE 的情境好像比較少碰到，不知道為何

> however, it is not all external systems that have support for idempotency keys and under such circumstances, you need to take additional measures for your requests to be idempotent. You will see how this can be accomplished through atomic phases shortly.

這篇寫得有點怪怪的，好像把 Txn 的 atomicity, rollback, recover 和中間各層/自己的 idempotency 混在一起講了

不太 ideal, 我覺得 service oriented 的幂等性比較常討論
