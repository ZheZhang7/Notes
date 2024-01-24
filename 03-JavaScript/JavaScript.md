# JavaScript

## JS 数据类型：

number string boolean null undefined symbol object array

**null 和 undefined 区别**：null 表示没有，undefined 有对象但是没值

## JS 判断数据类型方法：

1. typeof: 判断出来 number string boolean undefined function 无法判断 null array object 均为 object
2. instanceof：检测构造函数（右边）的  prototype  属性是否出现在某个实例对象（左边）的原型链上。因此只能判断复杂类型
3. object.property.tostring.call()：不能检测非原生构造函数的构造函数名。其余都可以
4. constructor：不能判断 null 和 undefined。不安全的。因为 constructor 指向可以改变。

## 字符串方法：

由于字符串的不可变性，所有方法都不会改变原字符串

**查找字符串**

1. indexof/lastIndexOf: 查找想要的字符串，返回索引值，否则返回-1
2. search：查找字符串，返回索引值
3. includes: 字符串中是否包含指定的内容，返回布尔值
4. startswith：字符串是否指定的内容开头，返回布尔值
5. endswith：字符串是否指定的内容结尾，返回布尔值

**获取指定位置的字符**

1. charAt
2. charCodeAt

**字符串截取**

1. slice：左闭右开，可以传负值，从后开始，返回新字符串
2. substring：左闭右开，不能为负数，返回新字符串
3. substr：开始索引，截取的长度；返回新字符串
4. split：返回数组；split(',')以逗号分隔
5. trim：去掉前后空格

## 数组方法

**不会改变原数组**

1. slice：左闭右开，可以传负值，从后开始，返回新数组
2. concat：合并数组，返回新数组
3. join：数组转为字符串，返回转换后的字符串
4. indexOf/lastIndexOf/includes
5. find：找到第一个满足条件返回 true 的元素
6. findIndex
7. every/some: 全满足返回 true/只要有一个满足返回 true
8. map：映射，对每一项进行加工，返回新数组
9. filter：返回为 true 的项，组成新数组

**改变原来数组**

1. push：尾部添加，返回新数组长度
2. pop：尾部删除，返回被删除元素
3. unshift：头部添加，返回新数组长度
4. shift：头部删除，返回被删除元素
5. splice：删除指定元素，返回被删除元素
6. fill：填充数组，返回新数组
7. reverse：反转数组
8. sort：从小到大排列
9. [reduce](https://blog.csdn.net/w17624003437/article/details/125858710)

## 对象方法

1. defineProperty(obj, property, detail): detail 包含 value configable writable enumerable
2. assign：浅 copy，enumerable 为 false 不能 copy
3. create：使用指定对象原型创建一个新的对象
4. keys：可枚举属性的数组，与 for...in 区别在于得到原型链上的属性
5. freeze：浅冻结一个对象
6. is：比较两个对象是否相等 Object.is(NaN,NaN)-->true, Object.is(+0,-0)-->false, ===结果相反

## 预编译

**JS 运行：语法分析、预编译、解释执行**

1. 页面产生便创建 GO 全局对象（Global Object）window 对象
2. 第一个脚本文件加载
3. 语法分析
4. 开始预编译，查找变量声明作为 GO 属性，值为 undefined
5. 查找函数声明，值为函数体（作为 GO 属性）

执行函数预编译

1. 创建 AO 对象（active object）执行期上下文
2. 查找形参和变量声明，值为 undefined
3. 实参值赋给形参
4. 查找函数声明，值为函数体

函数声明 > 形参 > 变量声明

## this apply call bind

this 函数执行期的上下文对象，根据函数调用方式不同，this 指向不同的对象

1. 以构造函数的形式调用时，this 指向实例对象
2. 使用 call 和 apply、bind 调用时，this 指向指定的对象
3. 以方法的形式调用时，this 指向调用方法的那个对象
4. 函数的形式调用时，this 指向 window
5. 事件绑定函数的调用时，this 指向绑定事件的对象

**apply、call、bind 比较**

call：修改 this 指向，调用函数。可以实现继承。参数：this 指向，实参 1，2...<br />
apply：修改 this 指向，调用函数。参数：this 指向，[实参数组]<br />
bind: 修改 this 指向，不调用函数。参数：this 指向，实参 1，2...<br />

## 原型和原型链

prototype 为原型对象、**proto**对象原型

![原型和原型链.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4cd296c075174ee7a9dc168827bde17d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=987&h=720&s=48689&e=png&b=ffffff)

## 通过 new 方法创建对象的过程

[参考链接](https://juejin.cn/post/6968856664560648199?searchId=202308031023363F56C2EA2943E82A12B3)

1. 首先创建一个新对象
2. 将新对象的**proto**指向构造函数的原型 <br />
   它将新生成的对象的**proto**属性赋值为构造函数的 prototype 属性，使得通过构造函数创建的所有对象可以共享相同的原型。这意味着同一个构造函数创建的所有对象都继承自一个相同的对象，因此它们都是同一个类的对象。
3. 改变 this 指向，指向空对象
4. 对构造函数的返回值做判断，返回相应值<br />
   返回值是对象类型则直接返回，基本类型则返回空对象

## 闭包

有权访问另一个函数作用域中变量的函数

**闭包的作用**：

1. 延伸变量的作用范围
2. 私有化变量
3. 模拟块级作用域
4. 创建模块

```js
function fn1() {
  let a = 10;
  function fn2() {
    console.log(a);
  }
  return fn2;
}
const res = fn1();
res();
```

**缺点**：fn1 执行后，变量 a 不会立即销毁，因为 fn2 中还要继续调用变量 a；导致变量一直在内存中，过多的闭包导致内存泄漏

## 拷贝

**浅 copy 指向同一个地址**

1. Object.assign(obj1, obj2)
2. 展开运算符 ...
3. concat
4. slice

**deepClone**

```js
function deepClone(newobj, obj) {
  for (let key in obj) {
    if (obj[key] instanceof Array) {
      newobj[key] = [];
      deepClone(newobj[key], obj[key]);
    } else if (obj[key] instanceof Object) {
      newobj[key] = {};
      deepClone(newobj[key], obj[key]);
    } else {
      newobj[key] = obj[key];
    }
  }
}
```

## 继承

[参考链接](https://juejin.cn/post/6844903475021627400?searchId=20230807143311888CD30AB46F4E172FEE)

## DOM

**DOM 操作**

1. 创建节点：createElement
2. 插入节点：parentNode.appendChild parentNode.insertBefore
3. 删除节点：parentNode.removeChild
4. 复制节点：cloneNode

**节点属性**

1. 获取节点属性：ElementNode.getAttribute(key)
2. 设置节点属性：ElementNode.setAttribute(key, value)
3. 删除节点属性：ElementNode.removeAttribute(value)

**js 动画**

1. offset 元素尺寸
2. scroll 滚动
3. client 可视区大小
4. scrollheight - scrolltop = clientheight

## BOM

**对象**

1. window
2. Navigator：当前浏览器信息，识别不同浏览器
3. Location：浏览器地址栏信息
4. History：浏览器历史记录
5. Screen：屏幕信息

**常用方法**

1. history.length
2. history.back()
3. history.forward()
4. history.go()
5. location.href
6. location.reload

## let const

let：定义变量<br />
const：定义常量（定义后，不可修改）

**特点**：

1. 不属于顶层对象
2. 不允许重复声明
3. 不存在变量提升
4. 暂时性死区（TDZ，仅针对当前作用域有效）
5. 支持块级作用域

## 箭头函数

**特点**

1. 写法简单
2. this，本身无 this，内部 this 就是外部代码的 this，因此也无法使用 call 修改 this 指向
3. 函数声明和箭头函数都有参数默认值

**剩余参数和 arguments 比较**

1. 剩余参数普通函数和箭头函数都可以使用，arguments 只能普通函数使用
2. 剩余参数包含没有对应形参的实参，arguments 包含所有参数
3. 剩余参数是真实数组，arguments 是类数组对象
4. 剩余参数更加灵活

## set map

**set**

1. 类似数组，允许存储任意数据类型，但是不能重复
2. set 常用方法：get size set delete has clear

**map**

1. 用于存储映射关系
2. 与之前用于存储映射关系的对象相比，map 可以将对象作为 key 存储
3. 数组、字符串、map、set 可以使用 for...of 遍历，普通对象不能<br />
   备注：是否可以使用 for...of 遍历，要看原型上是否存在 Symbol.iterator 方法，普通对象没有这个方法

## Promise

**异步编程方案**

1. 解决回掉地狱
2. 管理异步操作更加容易

**Promise 状态**

1. 初始化 pending --> new Promise
2. 成功 fullfilled --> resolve()
3. 失败 rejected --> reject()

**Promise 实例方法**

1. then
2. catch
3. finally

**Promise 的静态方法**

1. Promise.resolve()
2. Promise.reject();
3. Promise.all([]) 并发处理多个异步任务，都成功 resolve 一个成功 reject
4. Promise.race() 并发处理多个异步任务 返回第一个处理的 Promise，且状态与第一个状态一致
5. Promise.allSettled() 多个异步，返回所有结果，适用于多个彼此不依赖的异步任务

[**手写实现 Promise 静态方法**](https://juejin.cn/post/7038371452084551694?searchId=202308091054551BC2848A05AA572309FD#heading-8)

## 事件循环补充

**异步操作**

1. 事件监听
2. 定时器 settimeout setinterval ajax
3. Promise Generator async/await

**异步任务**

1. 宏任务
2. 微任务：process.nextTick Promise.then/catch/finally Mutationobserver()dom 节点发生变化时调用

第一个宏任务是 script

**event loop**

1. 在宏任务队列中，按照入队顺序，找到第一个执行的宏任务，放入调用栈，执行
2. 执行完该宏任务队列下所有同步任务后，即调用栈清空，该宏任务被推出宏任务队列，然后微任务队列按照入队顺序执行，直到清空
3. 清空微任务队列，一个事件循环结束
4. 在宏任务队列中，找到下一个宏任务，开始执行下一个 event loop，直到宏任务队列清空

## Proxy Reflect

Proxy 可以对目标对象的读取，函数调用等操作进行拦截，操作代理对象

```js
let target = {
  name: "tom",
  age: 18,
};

let handler = {
  get: function (target, key) {
    console.log("get" + key);
    return target[key];
  },
  set: function (target, key) {
    console.log("set", +key);
    return target[key];
  },
};
let proxy = new Proxy(target, handler);
console.log(proxy.name);
console.log(proxy.age);
```

**proxy 常用拦截方法**

1. get --> get(target, propkey, [recevier]) recevier 操作所针对的对象
2. set --> set(target, propkey, value, [recevier])
3. has --> has(target, propkey) target 是否存在 propkey

**Reflect**

1. 对某些返回方法的结果进行修改
2. 使用函数的方式实现了 object 的命令式操作

**Reflect 方法**

1. get(target, name, recevier) 当 target 中存在 name，this 绑定 recevier
2. set(target, name, value, recevier)
3. has(obj, name) 是否存在
4. deleteProperty(obj, property) 删除 obj 的 property 属性
5. getPropertyof(obj)
6. defineProperty(target，name，attr)
