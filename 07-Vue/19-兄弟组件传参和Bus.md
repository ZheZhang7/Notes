# 兄弟组件传参和 EventBus

## 兄弟组件传参两种方案

- 借助父组件传参
- 使用 EventBus

### 借助父组件传参

```html
<template>
  <div>
    <a @on-click="getFalg"></a>
    <b :flag="Flag"></b>
  </div>
</template>

<script setup lang="ts">
  import A from "./components/A.vue";
  import B from "./components/B.vue";
  import { ref } from "vue";
  let Flag = ref<boolean>(false);
  const getFalg = (flag: boolean) => {
    Flag.value = flag;
  };
</script>

<style></style>
```

### 使用 EventBus

EventBus 就是发布订阅模式

发布订阅简写

```ts
type BusClass<T> = {
  emit: (name: T) => void;
  on: (name: T, callback: Function) => void;
};

type BusParams = string | number | symbol;
type List = {
    [key: BusParams]: Array<Function>
}

class Bus<T extends BusParams> implements BusClass<T> {
    list: List,
    constructor() {
        this.list = {}
    }
    emit(name: T, ...args: Array<any>) {
        let eventName: Array<Function> = this.list[name];
        eventName && eventName.forEach(ev => {
            ev.apply(this, args);
        });
    }
    on(name: T, callback: Function) {
        let fn: Array<Function> = this.list[name] || [];
        fn.push(callback);
        this.list[name] = fn;
    }
}

export default new Bus();
```
