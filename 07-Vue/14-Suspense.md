# Suspense 和异步组件

适合进行代码分包 Suspense 配合异步组件

简单案例骨架屏

定义数据

```json
{
  "data": {
    "name": "张三",
    "age": 30,
    "url": "https://www.example.com",
    "desc": "456"
  }
}
```

封装请求 axios

```ts
// 封装原生ajax
export const axios = {
  get<T>(url: string): Promise<T> {
    return new Promise<T>((resolve, reject) => {
      const xhr: XMLHttpRequest = new XMLHttpRequest();
      xhr.open("GET", url);
      xhr.onreadystatechange = () => {
        if (xhr.readyState === 4 && xhr.status == 200) {
          // 模拟请求时间较长 展示骨架屏
          setTimeout(() => {
            resolve(JSON.parse(xhr.responseText));
          }, 2000);
        }
      };
      xhr.send(null);
    });
  },
};
```

完成异步组件

```html
<template>
  <div class="sync">
    <div class="sync-content">
      <div><img :src="data.url" /></div>
      <div class="sync-pop">
        <div>{{ data.name }}</div>
        <div>&nbsp;{{ data.age }}</div>
      </div>
    </div>
    <hr />
    <div>{{ data.desc }}</div>
  </div>
</template>

<script setup lang="ts" module="es2021">
  import { ref, reactive } from "vue";
  import { axios } from "../server/axios";

  // 使用顶层await将整个组件变成异步组件
  interface Data {
    name: string;
    age: number;
    url: string;
    desc: string;
  }

  // ts ignore
  const { data } = await axios.get<Data>("./data.json");
</script>

<style></style>
```

获取异步组件，使用 suspense 实现代码分包

```html
<!-- 异步组件  代码分包 suspense -->
<template>
  <!-- 异步组件展示suspense -->
  <Suspense>
    <!-- 两个插槽 -->
    <template #default>
      <!-- 展示内容 -->
      <Child></Child>
    </template>
    <template #fallback>
      <!-- 站位 骨架屏 -->
      <Skeleton></Skeleton>
    </template>
  </Suspense>
</template>

<script setup lang="ts">
  import { ref, reactive, defineAsyncComponent } from "vue";
  import Skeleton from "./components/skeleton.vue";
  // 获取异步组件 defineAsyncComponent
  const Child = defineAsyncComponent(() => import("./components/sync.vue"));
</script>

<style></style>
```
