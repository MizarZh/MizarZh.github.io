# Npm Package 强制使用特定版本

## 起因
在写 [obsidian-mdx-dictoinary](https://github.com/MizarZh/obsidian-mdx-dictionary) 插件时，我在其中加入了一个 `express` 后端，结果旧遇到了问题：
```
require_streams(...) is not a function
```

查了很久，最后问题终结在这个 issue 上：[Not working with Karma and Webpack 4](https://github.com/ashtuchkin/iconv-lite/issues/204)。

问题出在一个叫做 [`iconv-lite`](https://github.com/ashtuchkin/iconv-lite) 的包上。出问题的语句： `require(&#34;./streams&#34;)(iconv);`。由于这个语句使用了两个连续的函数调用，打包库对这种写法不太高兴，这就是为什么 issue 中的 `webpack` 和 obsidian 插件使用的  `esbuild` 都报错了。

`iconv-lite` 作者已对此进行了更新，但是某一些包仍然使用了旧版的 `iconv-lite`。由于 `npm` 依赖十分复杂，我尝试了很多方法去解决这个问题。在经历了无数次试错之后，我发现只需要很简单的一个做法就可以解决这个问题：强制所有包使用 `iconv-lite` 的某个版本。

## 做法
对于低版本的 `npm`，我们可以使用 [npm-force-resolutions](https://github.com/rogeriochaves/npm-force-resolutions)。`yarn` 也支持类似的写法：

```js
// package.json
{
	...
	&#34;resolutions&#34;: {
		&#34;foo&#34;: &#34;1.0.0&#34;, // foo 版本强制为 1.0.0
		&#34;pac-a/pac-b&#34;: &#34;3.3.2&#34; // pac-a 下的 pac-b 应该使用 3.3.2 版本
	}
}
```


高版本的 `npm` 以及 `pnpm` 都支持 override 的写法：
```js
// package.json
{
	...
	&#34;overrides&#34;: {
		&#34;foo&#34;: &#34;1.0.0&#34;, // foo 版本强制为 1.0.0
		&#34;bar@2.0.0&#34;: {
			&#34;baz&#34;: 1.0.0 // 只有当 bar 为版本 2.0.0 时，才强制 baz 使用 1.0.0 版本
		}
	}
}
```

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/npm-package-%E5%BC%BA%E5%88%B6%E4%BD%BF%E7%94%A8%E7%89%B9%E5%AE%9A%E7%89%88%E6%9C%AC/  

