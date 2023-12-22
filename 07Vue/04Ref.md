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

   取值

   ```ts
   console.log(test.value.name);
   ```

   读取 dom 属性

   ```html
   <div ref="test"></div>
   ```

   ```ts
   const dom = ref<HTMLDivElement>();
   console.log(dom.value?.innerText);
   ```

2. isRef 判断是否是 ref 类型
3. shallowRef 浅层响应式

   ref 深层次， shallowRef 浅层响应式

   创建一个跟踪自身.value 变化的 ref，值不是响应式的

   指到 value title.value = { name: 'li' } 才会触发视图更新

   ref 和 shallowRef 不可以混用，否则会影响 shallowRef 造成视图的更新

4. triggerRef 强制更新视图

   ```ts
   triggerRef(test);
   ```

5. customRef 自定义 ref

   例子

   ```html
   <div>{{ test }}</div>
   <button @click="customclick">customclick</button>
   ```

   ```ts
   const MyRef = <T = any>(value: T) => {
     let timer: any;
     return customRef((track, trigger) => {
       return {
         get() {
           track();
           return value;
         },
         set(newValue: T) {
           clearTimeout(timer);
           timer = setTimeout(() => {
             console.log("trigger 成功");
             value = newValue;
             timer = null;
             trigger();
           }, 500);
         },
       };
     });
   };
   const test = MyRef<string>("zhang");
   const customclick = () => {
     test.value = "li";
   };
   ```
