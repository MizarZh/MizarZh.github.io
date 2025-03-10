# Python Profiling

## Python 性能分析
最简单的方法：datetime
```python
import datetime
start = datetime.datetime.now()
# do something
end = datetime.datetime.now()
print(start - end)
```

但是每个地方都要自己加确实有点麻烦。于是选择自动定位并测试函数的工具 `cProfile`
```python
import cProfile
cProfile.run(&#39;xxx&#39;)
```

然后你就会得到一个表格，其中记录了哪个部分用时多少。

但是这个包输出的表格不太好读，因此我们再转向另一个包：`pyinstrument`：
```python
from pyinstrument import Profiler

profiler = Profiler()

profiler.start()

HTML(ani.to_html5_video())

profiler.stop()

# profiler.output_text() # 输出 文本
# profiler.output_html() # 输出 html
profiler.open_in_browser() # 在浏览器中打开 html
```

这个包可以更加详细的分析各个部分的占比。

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/python-profiling/  

