# Ref 全家桶

1. ref 将包裹的对象变为响应式

   Ref 类型

   ```ts
   const test: Ref = ref({ name: zhang });
   ```

   泛型

   ```ts
   type Test = {
     name: string;
   };
   const test = ref<Test>({ name: zhang });
   ```
