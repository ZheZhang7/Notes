# computed

计算属性就是当依赖的属性的值发生变化的时候，才会触发他的更改，如果依赖的值，不发生变化的时候，使用的是缓存中的属性值。

```html
<template>
  <div>firstName: <input type="text" v-model="firstName" /></div>
  <div>lastName: <input type="text" v-model="lastName" /></div>
  <p>{{ firstName }} - {{ lastName }}</p>
  //通过计算属性实现 fullName: {{ fullName }}
  <div>
    <button @click="changeName">changeName</button>
  </div>
</template>

<script setup lang="ts">
  import { ref, computed, reactive } from "vue";
  let firstName = ref("zhang");
  let lastName = ref("san");

  // 选项式写法 支持get和set自定参数
  // let fullName = computed<string>({
  //   get(): string {
  //     return firstName.value + " - " + lastName.value;
  //   },
  //   set(newValue: string): void {
  //     [firstName.value, lastName.value] = newValue.split(" - ");
  //   },
  // });

  // const changeName = (): void => {
  //   fullName.value = "li - si";
  // };

  // 函数式写法  传入一个getter函数，不支持set修改
  let fullName = computed((): string => {
    return firstName.value + " - " + lastName.value;
  });
</script>
```

```html
// 案例
<template>
  <input
    v-model="keyWord"
    type="text"
    placeholder="search"
    style="margin-bottom: 20px"
  />
  <table border cellpadding="0" cellspacing="0" width="600">
    <thead>
      <tr>
        <th>物品名称</th>
        <th>物品单价</th>
        <th>物品数量</th>
        <th>物品总价</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in searchData">
        <td>{{ item.name }}</td>
        <td>{{ item.price }}</td>
        <td>
          <button @click="item.count > 1 ? item.count-- : null">-</button>
          {{ item.count }}
          <button @click="item.count < 99 ? item.count++ : null">+</button>
        </td>
        <td>{{ item.price * item.count }}</td>
        <td><button @click="del(index)">删除</button></td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td colspan="5">总价: {{ totalPrice }}</td>
      </tr>
    </tfoot>
  </table>
</template>

<script setup lang="ts">
  import { ref, reactive, computed } from "vue";

  interface Data {
    name: string;
    price: number;
    count: number;
  }

  let data = reactive<Data[]>([
    {
      name: "可乐",
      price: 3,
      count: 10,
    },
    {
      name: "雪碧",
      price: 2,
      count: 20,
    },
    {
      name: "苏打水",
      price: 1,
      count: 30,
    },
  ]);
  // 不使用计算属性
  // let totalPrice = ref(0)

  // const total = () => {
  //   totalPrice.value = data.reduce((prev: number, next: Data) => {
  //     return prev + next.price * next.count;
  //   }, 0)
  // }
  // total();

  // const del = (item: number): void => {
  //   data.splice(item, 1);
  //   total();
  // }

  // 使用计算属性优化  减少total函数调用
  // 仅仅取值  使用函数式写法
  const totalPrice = computed<number>(() => {
    return data.reduce((prev: number, next: Data) => {
      return prev + next.price * next.count;
    }, 0);
  });

  const del = (item: number): void => {
    data.splice(item, 1);
  };

  // 搜索
  const keyWord = ref<string>("");
  const searchData = computed<Data[]>(() => {
    return data.filter((item: Data) => item.name.includes(keyWord.value));
  });
</script>
```
