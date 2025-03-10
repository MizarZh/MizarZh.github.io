# Rust Diesel Sqlite3 错误

## 错误
一大团 lib 的错误，然后说 LNK1181 错误

1. 有可能是 msvc 安装上的问题。不过大部分应该都是正常的
2. **是否编译了 `sqlite3.lib` 文件**。

## 解决方法
1. 从 https://www.sqlite.org/download.html 下载 dll 包（内含 dll 文件和 exp 文件）和 tools 包（加入 PATH）。
2. cd 至 dll 包的文件夹。
3. 64位操作系统输入： `lib /DEF:sqlite3.def /OUT:sqlite3.lib /MACHINE:x64`；32位操作系统输入：`lib /DEF:sqlite3.def /OUT:sqlite3.lib /MACHINE:x86`，完成编译。
4. 找到输出的 `sqlite3` 文件，
	1. 放到 rust 使用的 msvc lib 的位置。
	2. 或者放到要使用这个文件的 cargo 中。
	3. 或者加入 PATH 变量。

## 参考资料
https://blog.itdevwu.com/post/915/

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/rust-diesel-sqlite3-lnk1181-%E9%94%99%E8%AF%AF/  

