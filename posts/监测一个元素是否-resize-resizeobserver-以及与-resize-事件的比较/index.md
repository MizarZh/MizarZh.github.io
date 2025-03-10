# 监测一个元素是否 Resize （ResizeObserver） 以及与 Resize 事件的比较

## ResizeObserver
监测元素的 resize 需要使用 [`ResizeObserver`](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver) 类：
```js
new ResizeObserver((entries) =&gt; {
  for (const x of iframe) {
    x.resize()
  }
  for(const entry of entries) {
    abc.size = entry.borderBoxSize
  }
}).observe(root)
```

其中 `ResizeObserver` callback 中的参数 `entries` 指的是这个 observer 正在观察元素的数组。

## 与 resize 事件的比较
[resize event](https://developer.mozilla.org/en-US/docs/Web/API/Window/resize_event)

文档视图（窗口）调整大小时会触发 **`resize`** 事件。现在 `resize` 事件只针对窗口（window）触发。

```js
window.onresize = (xxx) =&gt; {xxx}
```

即 `resize` 事件仅作用在窗口上，观测的是窗口大小的改变。而 `ResizeObserver` 作用在元素上，观测的是元素大小的改变。


---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/%E7%9B%91%E6%B5%8B%E4%B8%80%E4%B8%AA%E5%85%83%E7%B4%A0%E6%98%AF%E5%90%A6-resize-resizeobserver-%E4%BB%A5%E5%8F%8A%E4%B8%8E-resize-%E4%BA%8B%E4%BB%B6%E7%9A%84%E6%AF%94%E8%BE%83/  

