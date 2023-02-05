---
title: z-index 六条
description:
categories:
- weekly
tags:
---

### 六条基本规则

1. HTML 文档默认是 static 布局（通过 position 可以改变布局方式），除了 `<body></body>`，任何一个 DOM 节点也都有 position 值，如果没有设置，那么默认是 static

2. static 布局的元素，z-index 无效，文档按照 HTML 顺序渲染，后出现的 DOM 节点在先出现的 DOM 节点上面

3. 如果 DOM 元素是非 static 布局，那么 z-index 就会生效，z-index 的值越大，越靠上

4. 非 static 的 DOM 元素 `EleA` 会创建一个 stacking context，这个 stacking context 里面的元素永远在 `EleA` 上方

5. 以第 4 条 `EleA` 为例，只要是 `EleA` 的 "DOM 孩子" 都是 `EleA` 创建的 stacking context 的 "**context 孩子**"，并且层级是一样的。因此下面例子里 `EleB` 和 `EleC` 是同一层级（这里假设 `EleB` 没有新创建 stacking context）

```html
<div id="EleA">
    <div id="EleB">
        <div id="EleC"></div>
    </div>
</div>

<!-- 涉及的例子：https://codepen.io/web-dot-dev/pen/XWpeayj-->
```

5. 在比较 z-index 的情况下（非 static 布局），如果两个 DOM 节点在同一 stacking context，比较他们的 z-index，如果在不同 stacking context，比较创建这两个 stacking context 的 DOM 节点的 z-index

6. 创建 stacking context 的所有方法：https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context


### Debug 工具:

Edge 浏览器： 3D View （更推荐，缺点：不同的 stacking context 不能正确显示）

![](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230205233125.png)

Chrome：Layer (不是专门用来调试 z-index)

![](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230205233322.png)