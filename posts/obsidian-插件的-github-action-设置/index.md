# Obsidian 插件的 github action 设置

来源 [obsidian-tracker](https://github.com/pyrochlore/obsidian-tracker/blob/master/.github/workflows/releases.yml)

## 代码
```yaml
name: Release # action 的名字

on:
  push:
    tags:
      - &#34;*&#34; # 在 push tags 后触发 action
  
jobs:
  build:
    permissions: 
      contents: write # 给 action 写的权限
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Use Node.js
      uses: actions/setup-node@v4
      with:
        node-version: &#39;20.x&#39;
    - name: Build
      run: | # 这两句是核心，安装包&amp;构建程序
        yarn
        yarn run build
    - name: Release with Notes
      uses: softprops/action-gh-release@v1 # 使用了 gh-release 的包，快速生成文件
      with:
        files: |
          main.js
          manifest.json
          styles.css
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

[gh-relsease](https://github.com/softprops/action-gh-release)

## 如何发布
- `git push` 到 `remote`，注意 `package.json` 和 `manifest.json` 中的版本。
- 使用命令 `git tag x.x.x` 来标记 tag
- `git push origin x.x.x` （仅 push 一个版本） 或 `git push --tags` （push 所有版本）

## 注意事项
构建的过程是这样的（以推送 0.0.1 版本为例）：
- 推送到云端后，生成了一个 0.0.1 的分支。
- 接下来 actions 会根据这个 0.0.1 的分支构建。
- 如果说你更改了 main 的 workflow，这个 0.0.1 的分支仍然会按照之前的方式进行构建。

因此如果修改了 workflow，记得删除相应的分支再重新 push tags（20多次 commit 修改 release.yml 的痛）。

删除分支的方式：`git tag -d x.x.x`。

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/obsidian-%E6%8F%92%E4%BB%B6%E7%9A%84-github-action-%E8%AE%BE%E7%BD%AE/  

