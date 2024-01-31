# 自定义插件

- 向 Vue 添加全局级功能
- 如果是一个对象 需要有 install 方法 Vue 会帮你自动注入到 install 方法
- 如果是 function 就直接当 install 方法去使用

## 案例: 实现 Loading 插件

- 创建文件 components/loading/index.ts index.vue

- index.vue

```html
<template>
  <div v-if="isShow" class="loading">Loading...</div>
</template>

<script setup lang="ts">
  import { ref, reactive } from "vue";
  const isShow = ref<boolean>(false);

  const show = () => (isShow.value = true);

  const hide = () => (isShow.value = false);

  // 必须使用defineExpose暴露属性,才能在VNode.component?.exposed?
  defineExpose({
    show,
    hide,
    isShow,
  });
</script>

<style scoped lang="scss">
  .loading {
    background: black;
    opacity: 0.8;
    font-size: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    color: white;
  }
</style>
```

- main.js

```js
import Loading from "./components/Loading";

const app = createApp(App);

// 全局注册 Loading
app.use(Loading);
```

- index.ts

```ts
import type { App, VNode } from "vue";
import Loading from "../../App.vue";
import { createVNode, render } from "vue";

export default {
  // 必写install函数
  install(app: App) {
    // 创建虚拟节点
    const VNode: VNode = createVNode(Loading);
    // 将虚拟节点挂载到body上
    render(VNode, document.body);

    // 通过exposed读取api
    // VNode.component?.exposed;

    // 挂在到全局的app上
    app.config.globalProperties.$loading = {
      show: VNode.component?.exposed?.show,
      hide: VNode.component?.exposed?.hide,
    };

    console.log(VNode.component?.exposed);
  },
};
```

- 使用方法

```html
<template>
  <div></div>
</template>

<script setup lang="ts">
  import { ref, reactive, getCurrentInstance } from "vue";
  const instance = getCurrentInstance();
  instance?.proxy?.$Loading.show();
  setTimeout(() => {
    instance?.proxy?.$Loading.hide();
  }, 5000);

  // console.log(instance)
</script>
<style>
  * {
    padding: 0;
    margin: 0;
  }
</style>
```
