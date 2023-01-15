---
title: HTML 计算文字内容非换行宽度
description:
categories:
- weekly
tags:
---

有一些场景需要根据内容计算出非换行时的宽度，例如可以用来除以容器的宽度得出实际换行后的行数，这个问题有两个比较便捷的思路。

第一个思路是模拟容器DOM，为了和目标字体宽度一致，可以加上目标容器的类。同时为了拿到非换行的宽度，需要在虚拟的 DOM 设置 `white-space` 为非换行 `pre`。`position:fixed; right:-100px` 使虚拟 DOM 在页面不可见，不影响现在的页面显示。然后 `scrollWidth` 就可以拿到非换行宽度了。需要注意的是，为了让模拟的 DOM  `scrollWidth` 为有效值，必须把它挂到页面上，否则是 `0px`。 

第二个思路是通过 canvas 的 `measureText` 接口拿到内容宽度，但是在不同设备的表现不稳定。已知的一个原因是由于不同设备支持的字体不同，并不能直接确定浏览器最终兼容的是 css 中提供的 `font-family` 第几个 fallback，不同字体的字宽会导致误差。第一种思路经过验证更稳定，因此更推荐第一种，下面是两种思路的实现。

思路一：
```typescript
function getLineWidth(line: string) {
  const tempEle = document.createElement('div');
  const classOfTargetContainer = `example`;
  tempEle.classList.add(`${classOfTargetContainer}`);
  // HTMLElement should append to DOM in order to get scrollWidth, which is 0px otherwise
  this.domNode.appendChild(tempEle);
  tempEle.textContent = line;
  tempEle.style.width = '0px';
  tempEle.style.whiteSpace = 'pre';
  tempEle.style.position = 'fixed';
  // hide temp element
  tempEle.style.right = '-100px';
  const width = tempEle.scrollWidth;
  tempEle.remove();
  return width;
}
```

思路二：
```typescript
function getCtx() {
  const fontSize = window.getComputedStyle(this.domNode).fontSize;
  const fontFamily = window
    .getComputedStyle(this.domNode)
    .fontFamily.split(',')[0];
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  assertExists(ctx);
  ctx.font = `${fontSize} ${fontFamily}`;
  return ctx;
}

const ctx = this._getCtx();
const width = ctx.measureText(line).width;
```