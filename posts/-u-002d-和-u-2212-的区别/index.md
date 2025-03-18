# &#34;-&#34;(U&#43;002D) 和 &#34;−&#34;(U&#43;2212) 的区别

在处理数据时，本想 `split(&#39;-&#39;)` 将相应的参数分离出来，结果竟然发现这个 string 中竟然用了 `-` 和 `−` 两种不同的符号。它们的区别是什么？

## python 转换成 hex 表示
```python
# ord 返回这个字符的 unicode 数字表示
# hex 将数字转换成 16 进制
hex(ord(&#34;-&#34;)) # 0x2d
hex(ord(&#34;−&#34;)) # 0x2212
```

## 区别是什么
`- (0x2d)`：Hyphen，连字符。长度较短。这个字符是可以在键盘上找到的（在 0 和 = 之间）。
`− (0x2212)`： Minus Sign，减号。长度较长。这个字符才是真正的减号，但这个字符不能用键盘直接打出来。有很多时候这个字符和连字符被视作为同一个字符（latex 中这两个都被渲染成减号，浏览器搜索这两个符号都匹配到一样的结果）

## 其他的减号
- the hyphen-minus (-, ASCII 45, next to the zero key)
- the en-dash (–, Unicode 2013, Alt&#43;0151 on Windows)
- the em-dash (—, Unicode 2014, Alt&#43;0150 on Windows)
- the true minus (−, Unicode 2212)

https://benjaminwolfe.github.io/signs/


---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/-u-002d-%E5%92%8C-u-2212-%E7%9A%84%E5%8C%BA%E5%88%AB/  

