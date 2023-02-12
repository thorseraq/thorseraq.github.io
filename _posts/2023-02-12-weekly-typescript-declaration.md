---
title: TypeScript declaration
description:
categories:
- weekly
tags:
---

- TypeScript 的内置类型的声明在 `node_modules/typescript/lib/lib.[something].d.ts`，并且不需要提供 `.js` 实现，实现是内置的。

- 第三方包需要提供 `.js` 实现， `.js` 实现可以是由 `.ts` transpile 生成的，也可以是直接写的。（BlockSuite 本身是 TypeScript 项目，所以 `.js` 实现是 transpile 生成的，并且通过 exports 把源文件指到了 ./dist 文件夹。@blocksuite/blocks 包是通过 tsc 打包，因此输出位置是通过 tsconfig.json 指定的）

   <img src="https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230206103947.png" style="zoom:33%;" />

   <img src="https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230206104152.png" style="zoom:33%;" />

- 根据代码最后的执行环境实际包括哪些 api，需要判断包括哪些内置`lib.[something].d.ts`。可以通过 [target](https://www.typescriptlang.org/tsconfig#target) 或者 [lib](https://www.typescriptlang.org/tsconfig#lib)（更精细）控制。

<img src="https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230206104842.png" style="zoom:33%;" />

- 如果是 TypeScript项目 可以使用 tsc 生成类型文件，如果是 JavaScript，需要手动补充类型文件。

- 类型声明文件可以随 npm 包一起发，位置[通过 package.json 的 `types` 或者 `typing` 字段指定](https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html#including-declarations-in-your-npm-package)。也可以单独发在 DefinitelyTyped 仓库，@types/name, `name` 和 package 名称保持一致。（注意 package.json 文件里 types 可以是根节点，exports，或者 publishConfig 下的 key）

- TypeScript 自动寻找的类型位置：

    1. `node_modules/@type`，`../node_modules/@type`, `../../node_modules/@type` 参考 [TypeScript: TSConfig Reference - Docs on every TSConfig option (typescriptlang.org)](https://www.typescriptlang.org/tsconfig#typeRoots) （这个场景是用第三方库）

    2. tsconfig.json 里 [include](https://www.typescriptlang.org/tsconfig#include) 字段指定的 .d.ts file，（这个场景是在自己的库额外手写 .d.ts file，例 [blocksuite/index.d.ts at 1ad53ccbae9e08a294694204b8fcc5719f7b1d10 · toeverything/blocksuite · GitHub](https://github.com/toeverything/blocksuite/blob/1ad53ccbae9e08a294694204b8fcc5719f7b1d10/packages/global/index.d.ts)）

  ![image-20230206115449900](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/image-20230206115449900.png)