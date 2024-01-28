# 深入 v-model

- v-model 是 props 和 emit 组合而成
- prop： value-->modelValue
- 事件： input-->update:modelValue
- v-bind 的.sync 修饰符和组件的 model 选项已移除
- 新增支持多个 v-model
- 新增 支持自定义 修饰符 Modifiers

父组件

```html
<template>
  <div>
    <h1>父组件</h1>
    <div><button @click="isShow = !isShow">开关</button></div>
    <div>isShow:{{ isShow }}</div>
    <div>text:{{ text }}</div>
    <hr />
    <vModelVue v-model:textVal="text" v-model="isShow"></vModelVue>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive } from "vue";
  import vModelVue from "./components/v-model.vue";

  const isShow = ref<boolean>(true);

  // 绑定多个
  const text = ref<string>("zhang");
</script>
```

子组件

```html
<template>
  <div v-if="modelValue" class="modal">
    <div class="close"><button @click="closeM">关闭</button></div>
    <h3>子组件</h3>
    <div>内容：<input @input="change" :value="textVal" type="text" /></div>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive, onUpdated } from "vue";

  defineProps<{
    modelValue: boolean;
    textVal: string;
  }>();

  const emit = defineEmits(["update:modelValue", "update:textVal"]);

  const closeM = () => {
    emit("update:modelValue", false);
  };

  const change = (event: Event) => {
    const target = event.target as HTMLInputElement;
    emit("update:textVal", target.value);
  };
</script>

<style scoped lang="scss">
  .modal {
    width: 500px;
    height: 300px;
    border: 5px solid #eee;

    .close {
      display: flex;
      justify-content: flex-end;

      button {
        width: 50px;
        height: 30px;
        background-color: #fff;
        border: none;
      }
    }
  }
</style>
```
