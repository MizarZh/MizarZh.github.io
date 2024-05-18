# js 解析以及文本化 DOM 元素的做法

## 解析 DOM 文本
使用 [DOMParser](https://developer.mozilla.org/en-US/docs/Web/API/DOMParser)：
```js
const parser = new DOMParser();

const xmlString = &#34;&lt;warning&gt;Beware of the tiger&lt;/warning&gt;&#34;;
const doc1 = parser.parseFromString(xmlString, &#34;application/xml&#34;);

const htmlString = &#34;&lt;strong&gt;Beware of the leopard&lt;/strong&gt;&#34;;
const doc2 = parser.parseFromString(htmlString, &#34;text/html&#34;);
```

`parser.parseFromString` 返回类型为 `Document`，这种格式含有完整的 `&lt;head&gt;&lt;body&gt;` 元素，也无法直接打印，也没有 `toString` 方法，总之就是无法直接转换成文本。

## 文本化 DOM 元素
我翻了半天都没找到将 DOM 元素变成文本的函数。最后问了 chatgpt 才知道原来精髓就在类型本身：
```js
doc.documentElement.innerHTML // DOM 文本
```


---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/js-%E8%A7%A3%E6%9E%90%E4%BB%A5%E5%8F%8A%E6%96%87%E6%9C%AC%E5%8C%96-dom-%E5%85%83%E7%B4%A0%E7%9A%84%E5%81%9A%E6%B3%95/  

