# Windhawk 折腾（半失败：改变任务栏的背景颜色）

在使用 windows 11 的时候，我发现它总是默认折叠同一程序。我对此很不适应，因为我通常会打开多个 obsidian 和 vscode 的窗口，在它们之间切换十分麻烦。

经过一番摸索，发现 windows 11 有自带的不折叠选项，但是各任务栏的长度竟然是不一样的！
{{&lt;figure src=&#34;/images/Pasted image 20250309154518.png&#34; title=&#34;Pasted image 20250309154518.png&#34;&gt;}}

这让我十分不爽，想要回到 windows 10 的不折叠选项（保持相同长度）。
{{&lt;figure src=&#34;/images/Pasted image 20250309154657.png&#34; title=&#34;Pasted image 20250309154657.png&#34;&gt;}}

而我又很喜欢 windows 11 的任务栏风格。所以我开始四处寻找解决方法，最后找到了 Windhawk。

[Windhawk](https://windhawk.net/) 是一个修改 windows 外观的工具。其中的一个插件 [Taskbar Labels for Windows 11 - Windhawk](https://windhawk.net/mods/taskbar-labels) 可以设置任务栏不合并 &#43; 相同长度的任务栏标签长度，且保留 windows 11 的风格。至此我十分满意，总算是找到一个可行的方案了。

接下来我又有了一个想法：因为 windows 11 的单个图标风格比显示标签会更加好看，我能否定制某一个程序使用不同的颜色呢？

既然是自定义样式，我就转向了另一个插件 [Windows 11 Taskbar Styler - Windhawk](https://windhawk.net/mods/windows-11-taskbar-styler)。这个插件主要是用来调整整个任务栏的样式风格的，但是我尝试用它自定义单个程序使用的不同颜色。

自定义guide： [ramensoftware/windows-11-taskbar-styling-guide](https://github.com/ramensoftware/windows-11-taskbar-styling-guide/blob/main/README.md)

查看样式代码：[UWPSpy - Ramen Software](https://ramensoftware.com/uwpspy)

通过这两个教程，我找到了定制某一个程序背景颜色的方法（半成功）：

```css
/* Target */
Taskbar.TaskListButton[AutomationProperties.Name=Anki - 1 个运行窗口] &gt; Taskbar.TaskListLabeledButtonPanel &gt; Border#BackgroundElement
/* Styles */
Background=#000000
CornerRadius=5
```

即：选择窗口名为 `Anki - 1 个运行窗口` 的程序，找到 `background` 所对应的元素，设置颜色为 `#000000`。

这种方法的缺点在于只要程序名字一变就无法进行设置了。Windhawk 也没有提供 css `[x~=x]` 的 selector，因此只能暂时搁置。

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/windhawk-%E6%8A%98%E8%85%BE%E5%8D%8A%E5%A4%B1%E8%B4%A5%E6%94%B9%E5%8F%98%E4%BB%BB%E5%8A%A1%E6%A0%8F%E7%9A%84%E8%83%8C%E6%99%AF%E9%A2%9C%E8%89%B2/  

