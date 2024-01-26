# 依赖注入 Provide/Inject

provide 可以在祖先组件中指定我们想要提供给后代组件的数据或方法。在后代组件中，我们可以使用 inject 来接收这些暴露的属性。

使用场景

- 当父组件有很多数据需要分发给其子组件时，就可以使用 provide 和 inject。

注意

- 你如果传递普通的值 是不具有响应式的 需要通过 ref reactive 添加响应式

父组件

```html
<template>
  <div>
    <h1>App 爷爷组件</h1>
    <label>
      <input type="radio" value="red" name="color" v-model="colorVal" />红色
    </label>
    <label>
      <input type="radio" value="blue" name="color" v-model="colorVal" />蓝色
    </label>
    <label>
      <input type="radio" value="yellow" name="color" v-model="colorVal" />黄色
    </label>
    <div class="box"></div>
  </div>
  <hr />

  <a></a>
</template>

<script setup lang="ts">
  import { ref, reactive, provide } from "vue";
  import A from "./components/provider/A.vue";
  const colorVal = ref<string>("red");
  provide("color", colorVal);
</script>

<style scoped>
  .box {
    width: 100px;
    height: 100px;
    border: 1px solid #000;
    background-color: v-bind(colorVal);
  }
</style>
```

子组件

```html
<template>
  <div>
    <h1>A 子组件</h1>
    <div class="box"></div>
  </div>
  <hr />
  <b></b>
</template>

<script setup lang="ts">
  import { ref, reactive, inject } from "vue";
  import type { Ref } from "vue";
  import B from "./B.vue";
  const color = inject<Ref<string>>("color");
</script>

<style scoped>
  .box {
    width: 100px;
    height: 100px;
    border: 1px solid #000;
    background-color: v-bind(color);
  }
</style>
```

孙子组件

```html
<template>
  <div>
    <h1>B 子组件</h1>
    <div class="box"></div>
    <button @click="change">修改 color</button>
  </div>
  <hr />
</template>

<script setup lang="ts">
  import { ref, reactive, inject } from "vue";
  import type { Ref } from "vue";

  const color = inject<Ref<string>>("color");
  const change = () => {
    color!.value = "red";
  };
</script>

<style scoped>
  .box {
    width: 100px;
    height: 100px;
    border: 1px solid #000;
    background-color: v-bind(color);
  }
</style>
```
