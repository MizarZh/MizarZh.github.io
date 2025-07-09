# Electron 基础设置（react 和 Drizzle）

## 创建项目
提供 `npm create` 的 electron 项目有：
- [electron forge](https://www.electronforge.io/)：可以打包和创建项目，但是由于 Vite 是实验状态，因此不打算使用其进行项目的创建
- [electron vite](https://electron-vite.org/)：使用的这个，方便又快捷，还可以选择使用的框架。

```
pnpm create @quick-start/electron
```

选择 `react` 或 `react-ts` 框架即可。

## 项目结构
```
src/
├── main/
	├── index.ts
├── preload/
	├── index.ts
	├── index.d.ts
├── renderer/
│   ├── src/
│   └── App.tsx
```

简单总结一下 electron 的结构：
- main 即主程序（浏览器），可以进行本地文件的访问。
- preload 是每个（浏览器）页面载入前执行的指令。通常会通过 ipc 暴露 main 中的 api，使得页面能够访问主程序中的 api。api 通常会挂载到 `window` 对象上。
- renderer 是渲染线程，可通俗理解为页面和 UI。

## 配置
我通常会在 `src` 下加入 `shared` 文件夹，这样可以方便 electron 各部分查询类型。其下有两个文件： `types.ts` 是程序用到的类型，`schema.ts` 是数据库的类型。

```ts
// src/shared/types.ts
export interface posts {
	id: string
	title: string
	author: string
	content: string
}

// src/shared/schema.ts
import { sqliteTable, text, integer, real } from &#39;drizzle-orm/sqlite-core&#39;

export const books = sqliteTable(&#39;posts&#39;, {
  id: text(&#39;id&#39;).primaryKey(),
  title: text(&#39;title&#39;).notNull(),
  author: text(&#39;author&#39;),
  content: text(&#39;content&#39;).notNull(),
})
```

在 `src/main/db.ts` 中，定义数据库操作相关的 api：

```ts
// src/main/db.ts
export class StorageService {
	private static db
	// drizzle createClient 和 migrate 之类的就在这里进行
	private static async initDb() {
		if (this.db) return this.db
		const db = createClient({ url: ... })
		this.db = drizzle(db)
		await migrate(this.db, { migrationsFolder: &#39;./drizzle&#39; })
		return this.db
	}
	// 具体的数据库操作方法
	private static async getPosts() {
		const db = await this.initDb()
	    const result = await db.select().from(books)
	    return result
	}
	...
}
```

定义完成后，就可以将数据库 api 映射到 IPC 中（）：

```ts
// src/main/index.ts

app.whenReady().then(() =&gt; {
	...

	ipcMain.handle(&#39;storage:getPosts&#39;, async () =&gt; {
	    return await StorageService.getPosts()
    })

	... // 可以定义更多
})
```

`ipcMain` 定义完了，另一端的 `ipcRenderer` 在 preload 中定义：

```ts
// src/preload/index.ts
const api= {
	storage: {
		getPosts: () =&gt; ipcRenderer.invoke(&#39;storage:getPosts&#39;),
		...
	}
}

// 将 api 放到 renderer 的 window 中
contextBridge.exposeInMainWorld(&#39;api&#39;, api)

// src/preload/index.d.ts
import { ElectronAPI } from &#39;@electron-toolkit/preload&#39;
import { api } from &#39;./index&#39;

declare global {
  interface Window {
    electron: ElectronAPI
    api: typeof api // 直接使用 typeof api，懒得再手写了
  }
}
```

最后，在 `renderer` 中就可以调用 `window.api.getPosts` 与数据库交互了。

## 如何快捷访问 `shared`
如果不用绝对路径访问 `shared` 文件夹，则会导致很多跨越多个部分的相对路径导入。为了实现绝对路径引入，需要修改 `tsconfig.json, tsconfig.node.json, tsconfig.web.json` 文件（后两个即为主进程和渲染进程的 `tsconfig`）。

首先需要给 `tsconfig.node.json, tsconfig.web.json` 文件中的 `&#34;include&#34;` 属性都加上 `&#34;src/shared/*.ts&#34;`。其次需要给它们两个添加 `compilerOptions`：
```json
  &#34;compilerOptions&#34;: {
    &#34;paths&#34;: {
      &#34;@shared/*&#34;: [&#34;src/shared/*&#34;]
    }
  }
```

这样就可以通过引用 `@shared/types` 来直接引用 `shared` 文件夹中的属性了。实际上许多高级项目中使用的 `@/xxx` 也是这么实现的。例如我想在 `renderer` 中也实现类似的效果：

```json
  &#34;compilerOptions&#34;: {
    &#34;paths&#34;: {
      &#34;@/*&#34;: [&#34;src/renderer/src/*&#34;],
      &#34;@shared/*&#34;: [&#34;src/shared/*&#34;]
    }
  }
```

那么也可以直接通过 `@/component/xxx` 之类的绝对路径访问相关的组件了。

---

> 作者: MizarZ  
> URL: http://localhost:1313/posts/electron-%E5%9F%BA%E7%A1%80%E8%AE%BE%E7%BD%AEreact-%E5%92%8C-drizzle/  

