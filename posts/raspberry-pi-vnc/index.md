# Raspberry pi VNC

## VNC 的问题
上次用 VNC 的时候还是在上次，回来之后发现 VNC 已经变成收费的模样了。这可不行，要用就必须用免费的 VNC。

## 使用 tightvnc
[Tightvnc](https://www.tightvnc.com/) 是免费且开源的 VNC，在树莓派上安装：
```bash
sudo apt install tightvncserver
```

记得关闭原来的 real-vnc：
```bash
sudo raspi-config # 选择 interface - vnc - 关闭
```

启动 vncserver：
```bash
vncserver :1
# vncserver :2 # 启动第二个
```

默认端口是 5900 &#43; 窗口。例如 `vncserver :1` 的端口就是 5901。

## 访问
Windows: 从 [Tightvnc](https://www.tightvnc.com/) 下载 viewer，然后连接访问即可。

我使用的是 mobaXterm 因此没有什么访问上的问题。

## 一些有用的命令
### `Too many authentication failures` 错误
如果出现了 `Too many authentication failures` 错误，意味着可能有多个 vnc 实例正在运行，我们可以用以下方法解决冲突的问题：
```
$ pgrep vnc

4456
45890

$ kill 4456
$ kill 45890

$ vncserver
// your vnc server will start
```

## Reference
https://pimylifeup.com/raspberry-pi-vnc-server/
https://askubuntu.com/questions/973055/too-many-authentication-failures-vnc-server
https://www.raspberrypi.com/documentation/computers/remote-access.html


---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/raspberry-pi-vnc/  

