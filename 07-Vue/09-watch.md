# watch

1. watch 需要侦听特定的数据源，并在单独的回调函数中执行副作用
2. watch 第一个参数监听源
3. watch 第二个参数回调函数 cb（newVal,oldVal）
4. watch 第三个参数一个 options 配置项是一个对象 immediate:true //是否立即调用一次 deep:true //是否开启深度监听
5. 监听多个 ref 变成[]
6. 监听 reactive 默认开启 deep
7. 监听对象的单一属性 监听源：() => message3.value.foo.bar.name

```html
<template>
  <div>case1: <input type="text" v-model="message1" /></div>
  <div>case2: <input type="text" v-model="message2" /></div>
  <div>case3: <input type="text" v-model="message3.foo.bar.name" /></div>
</template>

<script setup lang="ts">
  import { ref, reactive, watch, computed } from "vue";
  let message1 = ref("1");
  let message2 = ref("2");
  watch([message1, message2], (newval, oldval) => {
    console.log(newval, oldval);
  });
  let message3 = ref({
    foo: {
      bar: {
        name: "123",
      },
    },
  });
  watch(
    () => message3.value.foo.bar.name,
    (newval, oldval) => {
      console.log(newval, oldval);
    },
    {
      deep: true,
    }
  );
  // 监听对象里面的某一个属性，第一个参数需要() => message3.value.foo.bar.name

  // flush 控制 watch 执行顺序
  // pre组件更新之前调用 sync同步执行 post组件更新之后执行
</script>
```

# watchEffect

1. 默认调用一次
2. 返回一个函数 调用之后将停止更新
3. 触发监听之前会调用一个函数可以处理你的逻辑例如防抖
4. onTrigger 可以帮助我们调试 watchEffect

```html
<template>
  <div>
    case1: <input type="text" v-model="message1" /> case2:
    <input type="text" v-model="message2" />
    <button @click="stopWatch">stop</button>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive, watchEffect } from "vue";
  let message1 = ref<string>("1");
  let message2 = ref<string>("2");
  // watchEffect 立即执行一次，然后监听依赖项的变化 返回值为一个停止监听函数
  let stop = watchEffect(
    (oninvalidate) => {
      console.log("message1", message1.value);
      console.log("message2", message2.value);
      oninvalidate(() => {
        console.log("oninvalidate");
      });
    },
    {
      flush: "pre",
      onTrigger(event) {
        // debugger
      },
    }
  );

  // stop();
  let stopWatch = () => stop();
</script>
```
