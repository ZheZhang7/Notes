# 自定义 Hooks

- vue2 中使用 mixins, vue3 使用 hooks
- 弊端:组件的 data、methods、filters 会覆盖 mixins 里的同名 data、methods、filters。

## 案例 1 实现图片转 base64

```ts
// hooks实现
import { onMounted } from "vue";

type Options = {
  el: string;
};

export function useBase64(options: Options): Promise<baseUrl: string> {
  return new Promise((resolve) => {
    onMounted(() => {
      let img: HTMLImageElement = document.querySelector(
        options.el
      ) as HTMLImageElement;

      img.onload = (): void => {
        resolve({
            baseUrl: base64(img);
        });
      };
    });

    const base64 = (img: HTMLImageElement): string => {
      const canvas = document.createElement("canvas");
      const ctx = canvas.getContext("2d");
      canvas.width = img.width;
      canvas.height = img.height;
      ctx?.drawImage(img, 0, 0, canvas.width, canvas.height);
      return canvas.toDataURL("image/png");
    };
  });
}
```

```html
<!-- 使用 -->
<template>
  <div>
    <img id="img" src="./assets/images/1.png" alt="" width="300" height="500" />
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive } from "vue";

  import { useBase64 } from "./hooks/index";
  useBase64({
    el: "#img",
  }).then((res) => {
    console.log(res.baseUrl);
  });
</script>

<style></style>
```

## 案例 2 支持 hook 和自定义指令 去监听 dom 宽高变化

1. 如何监听 dom 宽高变化
2. 如何使用 vite 打包 配置 vite.config
3. 如何发布 npm

初始化工程

- 首先

```shell

npm init -y

tsc --init

npm install vue

npm install vite

```

- 其次

```shell
创建 vite.config.js 用于打包

创建声明文件 index.d.ts
```

编码

```ts
//如何监听 dom 宽高变化
import { App } from "vue";

function useResize(
  el: HTMLElement,
  callback: (cr: DOMRectReadOnly, resize: ResizeObserver) => void
) {
  let resize: ResizeObserver;
  resize = new ResizeObserver((entries) => {
    for (let entry of entries) {
      const cr = entry.contentRect;
      callback(cr, resize);
    }
  });
  resize.observe(el);
}

// 支持指令
const install = (app: App) => {
  app.directive("resize", {
    mounted(el, binding) {
      useResize(el, binding.value);
    },
  });
};

useResize.install = install;

export default useResize;
```

配置 vite.config.js 打包

```js
// vite.config.js
// 默认打包后生成umd和es module
// umd支持amd cmd cjs 全局变量模式
import { resolve } from "path";
import { defineConfig } from "vite";

export default defineConfig({
  build: {
    lib: {
      // Could also be a dictionary or array of multiple entry points
      entry: "src/index.ts",
      name: "useResize",
    },
    rollupOptions: {
      // 确保外部化处理那些你不想打包进库的依赖
      external: ["vue"],
      output: {
        // 在 UMD 构建模式下为这些外部化的依赖提供一个全局变量
        globals: {
          useResize: "useResize",
        },
      },
    },
  },
});
```

```ts
// 配置声明文件 index.d.ts
declare const useResize: {
  (el: HTMLElement, callback: Function): void;
  install: (app: App) => void;
};

export default useResize;
```

```json
// 修改package.json
{
  "name": "vresize",
  "version": "1.0.0",
  "description": "",
  //   require
  "main": "dist/vresize.umd.js",
  //   import
  "module": "dist/vresize.mjs",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    // 配置打包命令
    "build": "vite build"
  },
  "author": "",
  //   发布的文件
  "files": ["dist", "index.d.ts"],
  "license": "ISC",
  "devDependencies": {
    "vite": "^5.0.12"
  }
}
```

- 最后发布 npm

1. 创建 npm 账号 npm adduser(官方源)
2. 登录 npm 账号 npm login
3. 发布 npm publish
