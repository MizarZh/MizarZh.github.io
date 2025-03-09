# 如何将求和矢量化（以多摆的公式为例）

## 起因
闲来无事，尝试去写一个多摆的模拟程序。为了效率，我尝试去矢量化计算二次导数。但是网上查到的资料只有拉格朗日方程代入坐标后算出的原式，没有矢量形式：

$$
\begin{align}
&amp;\sum_{k=1}^{n}\left[gl_{j}\sin(\theta_{j})m_{k}\sigma_{jk}&#43;m_{k}l_{j}^2\ddot{\theta}_{j}\sigma_{jk}\right.&#43; \\
&amp;(\sum_{q\ge k}^{n}m_{q}\sigma_{jq})l_{j}l_{k}\left(\sin\left(\theta_{j}-\theta_{k}\right)\dot{\theta}_{k}^2&#43;\phi_{jk}\cos\left(\theta_{j}-\theta_{k}\right)\ddot{\theta_k}\right)\Big]=0
\end{align}
$$

其中：
$$
\sigma_{jk}=\left\{\begin{array}{ll}0&amp;\quad j&gt;k\\1&amp;\quad j\leq k\end{array}\right. \qquad\phi_{jk}=\left\{\begin{array}{ll}0&amp;\quad j=k\\1&amp;\quad j\neq k\end{array}\right.
$$

（出处 https://arxiv.org/pdf/1910.12610 式 38，但不知道为啥原文章没有约掉中间的一大团东西）。

接下来需要将这一大团东西转换成 $Mx=b$ （这里的 $x$ 为 $\ddot{\theta}$） 的矢量形式。

## 方法
换个思路看求和：求和的对象是一个矩阵（张量），求和是在这个矩阵的某个方向上相加。

{{&lt;figure src=&#34;/images/Pasted image 20240518161558.png&#34; title=&#34;Pasted image 20240518161558.png&#34;&gt;}}

我们想要将这个矩阵变成 $Mx=b$ 的形式，即将 $x$ 项提取出来：

$$
\begin{pmatrix}
&amp;&amp;&amp;&amp;&amp;&amp; \\
&amp;&amp;&amp;&amp;&amp;&amp; \\
&amp;&amp;&amp;M_{ij}&amp;&amp;&amp; \\
&amp;&amp;&amp;&amp;&amp;&amp; \\
&amp;&amp;&amp;&amp;&amp;&amp;
\end{pmatrix}
\begin{pmatrix} \\
 \\
x_{j} \\
 \\
 \\
\end{pmatrix} = 
\begin{pmatrix} \\
 \\
b_{i} \\
 \\
 \\
\end{pmatrix} 
$$

依照此想法，我们可以假设求和所求矩阵的表达式为：

$$
M_{ij}=A_{ij}&#43;B_{ij}x_{ij}&#43;C_{ij}x_{j}
$$

其中 $x$ 为我们想要的矢量。

两个说明：
- 因为我们想要分离出 $x$，所以我们才这样分解
- 为什么没有更高阶项？因为我们想要的是线性方程。如果出现了更高阶项，那么就无法线性化，这种方法就是徒劳的。

回到原想法：在某个轴上相加。假设我们对 $j$ 轴相加并设为 0：
$$
\sum_{j} (A_{ij}&#43;B_{ij}x_{i}&#43;C_{ij}x_{j}) = 0
$$

显然 $b=-\sum_{j}A_{ij}$，而 $C_{ij}$ 是 $M$ 的一部分。

$B_{ij}x_{i}$ 这一项显得有些棘手。那么应该如何处理呢？

我们可以假设 $i$ 只有三行，我们就可以写出加法的形式：

$$
\begin{align}
&amp;A_{i1}&#43;B_{i 1}x_{i}&#43;C_{i 1}x_{1}&#43; \\
&amp;A_{i2}&#43;B_{i 2}x_{i}&#43;C_{i 2}x_{2}&#43; \\
&amp;A_{i3}&#43;B_{i 3}x_{i}&#43;C_{i 3}x_{3}=0 \\
&amp;\Rightarrow \sum_{j} (A &#43; C_{ij} x_{j}) &#43; \left( \sum_{j} B_{ij}\right)x_{i}=0
\end{align}
$$

我们可以发现，只有到了相应的式子时，$x_{i}$  才会出现。与这个性质类似的矩阵是单位矩阵。因此我们可以认为这一项在求和后的表现为：$\left( \sum_{j} B_{ij} \right)\delta_{ij}$。总结：

$$
\begin{align}
M &amp;= \left( \sum_{j}B_{ij} \right) \delta_{ij} &#43; C_{ij} \\
b &amp;= -\sum_{j}A_{ij}
\end{align}
$$

## 多摆的向量表达式
因此，多摆的向量化表达式为：
$$\begin{align} &amp; b=-\sum_{k=1}^{n}\left(gl_{j}\sin(\theta_{j})m_{k}\sigma_{jk}&#43;(\sum_{q\ge k}^{n}m_{q}\sigma_{jq})l_{j}l_{k}\sin\left(\theta_{j}-\theta_{k}\right)\dot{\theta}_{k}^2\right)\\  &amp; M=(\sum_{k=1}^{n}m_{k}l_{j}^2\sigma_{jk})\delta_{jk}&#43;(\sum_{q\ge k}^{n}m_{q}\sigma_{jq})l_{j}l_{k}\phi_{jk}\cos\left(\theta_{j}-\theta_{k}\right)\end{align}$$

## 如何在 python 中表达
为了产生矩阵，我们会重度使用 `numpy.ufnc.outer` 这个函数。

原则：
- 搞清楚坐标的顺序。有些符号并不关心坐标在前还是在后（例如加法乘法），但是有些是相关的（例如减法，多摆中的 $\sigma$ 函数）
- 表达式中有些看起来像是一维的，但实际上是一个矩阵。（例如  $\sum_{q\ge k}^{n}m_{q}\sigma_{jq}$，看起来很像加法后变成了向量，但实际上由于 $q$ 依赖于 $k$，因此出来的是一个矩阵）。

一些例子：
$$
\cos(\theta_{j}-\theta_{k})
$$

```python
# 注意顺序。如果想要表达 theta_k - theta_j 那就必须加上负号
np.cos(np.subtract.outer(theta, theta))
```

$$
l_{j}\sin(\theta_{j})m_{k}\sigma_{jk}
$$

```python
# 将 jk 分别合在一起后再相乘。如果无法分开的就把它当成独立的另一个矩阵再相乘
np.outer(l * np.sin(theta), m) * sigma
```

详情见项目： https://github.com/MizarZh/n-pendulum

## 参考
https://travisdoesmath.github.io/pendulum-explainer/
https://arxiv.org/pdf/1910.12610
https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/%E5%A6%82%E4%BD%95%E5%B0%86%E6%B1%82%E5%92%8C%E7%9F%A2%E9%87%8F%E5%8C%96%E4%BB%A5%E5%A4%9A%E6%91%86%E7%9A%84%E5%85%AC%E5%BC%8F%E4%B8%BA%E4%BE%8B/  

