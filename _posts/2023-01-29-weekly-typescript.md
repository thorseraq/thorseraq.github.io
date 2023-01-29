---
title: TypeScript - 1
description:
categories:
- weekly
tags:
---

[Implement the Stage 3 Decorators Proposal #50820](https://github.com/microsoft/TypeScript/pull/50820)

[Improve checking of in operator #50666](https://github.com/microsoft/TypeScript/pull/50666)

[Type inference in conditional types #21496](https://github.com/Microsoft/TypeScript/pull/21496)

[Conditional types #21316](https://github.com/microsoft/TypeScript/pull/21316)


下面的示例来自 [Improve checking of in operator #50666](https://github.com/microsoft/TypeScript/pull/50666)

```typescript
const sym = Symbol();

function f1(x: unknown) {
    if (x && typeof x === "object" && "a" in x && 1 in x && sym in x) {
        x;  // Record<"a", unknown> & Record<1, unknown> & Record<typeof sym, unknown>
        x.a;  // Ok
        x[1];  // Ok
        x[sym];  // Ok
    }
}

function f2(x: { a: string } | { b: string }) {
    if ("a" in x) {
        x;  // { a: string }
    }
    else if ("b" in x) {
        x;  // { b: string }
    }
    else {
        x;  // never
    }
}
```

结合作者的评论
![](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230129233324.png)