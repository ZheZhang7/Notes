# Reactive 全家桶

## reactive

1. ref 和 reactive 区别

   ref 支持所有类型，reactive 只支持对象类型 Array Object Set Map

   ref 取值需要.value, reactive 取值直接操作

   reactive 不可以直接赋值，会使 proxy 失效，破坏响应式

   解决方案：数组可以解构加上 push 方法 或者添加一个对象，把数组作为一个属性去解决

   ```html
   <form>
     <input v-model="form.name" type="text" />
     <br />
     <input v-model="form.age" type="text" />
     <br />
     <button @click.prevent="submit">submit</button>
   </form>
   <ul>
     <li v-for="item in arr">{{ item }}</li>
   </ul>
   <button @click="add">add</button>
   ```

   ```ts
   // 对象
   let form = reactive({
     name: "zhang",
     age: 18,
   });

   const submit = () => {
     console.log(form);
   };

   // 数组
   let arr = reactive<number[]>([]);

   const add = () => {
     // 异步返回数据如何处理
     setTimeout(() => {
       let res = [1, 2, 3];
       arr.push(...res);
       console.log(arr);
     }, 1000);
   };
   ```

## shallowReactive

shallowReactive 和 shallowRef 一致，到嵌套的第一层结束
