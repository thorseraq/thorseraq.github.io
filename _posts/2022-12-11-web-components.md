---
title: Web Component 思考
description:
categories:
 - weekly
tags:
---
Web Component 提供了自定义可以渲染在页面上的 DOM 节点的能力，一个基本的 Web Component 可以按下面的方式定义。它继承了 HTMLParagraphElement，所以可以像一个普通的 HTMLParagraphElement 一样使用。同时，也可以通过自定义的属性和方法来扩展。因此，一个 Web Component 可以是一个完整的组件，包括样式、行为和 DOM 结构。

例1：Customized built-in elements - WordCount
```typescript
class WordCount extends HTMLParagraphElement {
  constructor() {
    // Always call super first in constructor
    super();

    // Element functionality written in here
  }
}

customElements.define("word-count", WordCount, { extends: "p" }); 
```

JavaScript 的浏览器运行时（runtime）会根据 HTML 文档生成标准的 DOM 节点， 标准的 DOM 节点只存储节点相关的信息（图1），我们通常还需要写一些类管理业务状态，再根据业务状态操作 DOM 改变页面。例如，编辑器 Quill 使用 [Parchment](https://github.com/quilljs/parchment)  作为文档模型管理编辑器状态。可以说， Quill 对业务状态的世界观是建立在 Parchment 之上。虽然 DOM 才是对应用户可见的文档模型，但是 Quill 仅仅在第一环和最后一环，即初始化 Parchment（例2）和将编辑器内容渲染到页面时才会操作 DOM。

图1：HTMLParagraphElement

![HTMLParagraphElement](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20221211183849.png)

例2：Quill 初始化 Parchment
```typescript
//https://github.com/quilljs/parchment/blob/a64d23d35c14a0f14e7649ede2e24330919ee73d/src/blot/abstract/parent.ts#L64
public build(): void {
    this.children = new LinkedList<Blot>();
  	// Need to be reversed for if DOM nodes already in order
    Array.from(this.domNode.childNodes)
    .filter((node: Node) => node !== this.uiNode)
    .reverse()
    .forEach((node: Node) => {
    try {
      const child = makeAttachedBlot(node, this.scroll);
      this.insertBefore(child, this.children.head || undefined); // 内部使用链表而并非和 DOM 类似的树存储 Blot 节点
    } catch (err) {
      if (err instanceof ParchmentError) {
        return;
      } else {
        throw err;
      }
    }
  });
}
```

在拥有自定义 WebComponent 能力之前，我们接触 DOM 的场景大多是在”外部“，以 cilent 的视角操作 DOM 节点，这种方式和调用任何其他 api 没有区别。但是当定义 WebComponent 类的内部，`element.querySelector()` 变成了 `this.querySelector()`，`element.attachShadow()` 变成了 `this.attachShadow()`，除了可以在类内部调用常规的 DOM api，此时业务状态也可以添加到类的内部。同时，HTMLELement 也有生命周期钩子可以使用（例3），可以配合事件管理自身状态。通过组件自己管理业务状态和生命周期，大大增加了组件可以复用的可能性。如果组件需要接收外部的传入，也可以通过设置 [ShadowRoot.mode](https://developer.mozilla.org/en-US/docs/Web/API/ShadowRoot/mode) 打破组件自身的状态隔离，使组件对外可见，此时就更像一个 React Component。

例3：[LifeCycle example](https://github.com/mdn/web-components-examples/blob/3bf4d03ce56ce5098709a2693d2e9d92036ee5bc/life-cycle-callbacks/main.js#L2)

```typescript
class Square extends HTMLElement {
  // Specify observed attributes so that
  // attributeChangedCallback will work
  static get observedAttributes() {
    return ['c', 'l'];
  }

  constructor() {
    // Always call super first in constructor
    super();

    const shadow = this.attachShadow({mode: 'open'});

    const div = document.createElement('div');
    const style = document.createElement('style');
    shadow.appendChild(style);
    shadow.appendChild(div);
  }

  connectedCallback() {
    console.log('Custom square element added to page.');
    updateStyle(this);
  }

  disconnectedCallback() {
    console.log('Custom square element removed from page.');
  }

  adoptedCallback() {
    console.log('Custom square element moved to new page.');
  }

  attributeChangedCallback(name, oldValue, newValue) {
    console.log('Custom square element attributes changed.');
    updateStyle(this);
  }
}

customElements.define('custom-square', Square); // customElements 是在浏览器 window 中存在的对象
```

目前开发 WebComponent 可以使用 Lit 框架，比直接使用 customElements.define() 原生 api 开发体验好一些。

> 使用 Lit 框架创建 WebComponent 与直接使用原生的 customElements.define() API 有一些显著的差异。
>
> 首先，使用 Lit 框架可以让你使用更高级的语法来定义 WebComponent，例如模板字符串、装饰器和双向绑定等。这些特性可以大大简化 WebComponent 的定义过程，并且可以提高代码的可读性和可维护性。
>
> 其次，Lit 框架提供了一些额外的功能，可以让你更方便地管理 WebComponent 的生命周期和状态。例如，可以使用 `connectedCallback` 和 `disconnectedCallback` 方法来控制 WebComponent 的挂载和卸载过程，并且可以使用 `render` 函数来动态更新 WebComponent 的内容。
>
> 最后，Lit 框架还可以为 WebComponent 提供性能优化功能。例如，它可以自动跟踪 WebComponent 中的属性和状态的变化，并且只在必要时才更新 DOM 结构。这样可以避免不必要的 DOM 操作，提高 WebComponent 的性能。
>
> 因此，使用 Lit 框架创建 WebComponent 相对于使用原生的 customElements.define() API，具有更方便的语法、更强大的功能和更优秀的性能。
>
> 这是聪明的 ChatGPT 说的
