# Mitt

使用 Mitt 库代替 EventBus(在 Vue3 中，$on 等实例方法被移除)

## 安装

```shell
npm install mitt -S
```

## mitt 初始化

全局总线，Vue 入口文件 main.js 中进行全局挂载

```js
import { createApp } from 'vue';
import App from './App.vue';
import mitt from 'mitt';

const Mit = mitt();

// 使用ts注册 必须要拓展ComponentCustomProperties类型才能获得类型提示

declare module "vue" {
    export interface ComponentCustomProperties {
        $Bus: typeof Mit;
    }
}

const app = createApp(App);

// Vue3挂载全局API
app.config.globalProperties.$Bus = Mit;

app.mount('#app');
```

## 使用方法

- emit 派发
- on 监听
- off 取消监听
- clear 清空所有

A 组件派发(emit)

```html
<template>
  <div>
    <h1>A component</h1>
    <button @click="emit1">emit1</button>
    <button @click="emit2">emit2</button>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive, inject } from "vue";
  import { getCurrentInstance } from "vue";

  const instance = getCurrentInstance();
  const emit1 = () => {
    instance?.proxy?.$Bus.emit("on-num", 100);
  };

  const emit2 = () => {
    instance?.proxy?.$Bus.emit("on-num", 200);
  };
</script>

<style scoped></style>
```

B 组件监听(on)

```html
<template>
  <div>
    <h1>B component</h1>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive, inject } from "vue";
  import { getCurrentInstance } from "vue";

  const instance = getCurrentInstance();
  instance?.proxy?.$Bus.on("on-num", (num) => {
    console.log(num, "------------->B");
  });

  // 监听所有事件 on(*)
  instance?.proxy?.$Bus.on("*", (type, num) => {
    console.log(type, num, "------------->B");
  });

  // 移除监听事件 off
  instance?.proxy?.$Bus.off("on-num", Fn); //unListen

  // 清空所有监听
  instance?.proxy?.$Bus.all.clear();
</script>

<style scoped></style>
```
