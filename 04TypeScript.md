# TypeScript

## 基础类型

1. number string boolean void 等
2. void 表示没有函数返回值为空
3. 也可以将 undefined 和 null(非严格模式下) 赋值给 void

## 类型顺序

1. 顶层类型 unknown any
2. Object
3. Number String Boolean
4. number string boolean
5. 123 '124' ture
6. never

## 任意类型 any 和 unknown 区别

1. any 和 unknown 区别 any 任意，随便 unknown 不知道
2. any 和其余类型可以任意赋值，unknown 只能赋值给 any 和自身
3. unknown 无法读到对象的属性，any 可以读到对象任意属性，包括未定义的也不会报错 --- undefined
4. unknown 更加安全

## Object object {} 区别

1. Object 为原型链顶端，包含大部分数据类型 number string bool [] {} Function ， 不包含 null undefined 值
2. object 常用做泛型约束， 仅包含引用类型 [] {} Function
3. {} 字面量模式 建议少用 和 Object 一致

## interface 接口

1. 接口就是为对象定义形状，让数据的结构满足约束的格式
2. 重名的 interface 可以合并
3. 可选属性可以使用？
4. 只读属性使用 readonly
5. 假设后端传输的数据很多，不是所有数据我们都需要，这个时候可以使用索引签名[propName:string]定义剩余参数
6. 一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
7. 接口可以继承接口 extends

```
interface Person {
    b?: string,
    readonly a: string,
    [propName: string]: any;
    cb:()=>void
}

const person: Person = {
    a: "213",
    c: "123",
    cb:()=>{
        console.log(123)
    }
}
```

## 数组类型

1. 普通数组类型 number[] 或者 Array< number >
2. 二维数组类型 number[][] 或者 Array< Array < number > >
3. ...args

```
let fun = (...args:number[]) => {
    console.log(args)
}

fun(1,2,3);
```

4. arguments 类数组

```
function Arr(...args:any): void {
    console.log(arguments)
    //错误的arguments 是类数组不能这样定义
    let arr:number[] = arguments
}
Arr(111, 222, 333)



function Arr(...args:any): void {
    console.log(arguments)
    //ts内置对象IArguments 定义
    let arr:IArguments = arguments
}
Arr(111, 222, 333)

//其中 IArguments 是 TypeScript 中定义好了的类型，它实际上就是：
interface IArguments {
    [propName: string]:any,
    length: number;
    callee: Function;
}
```

## 联合类型 类型断言 交叉类型

1. 联合类型 联合类型表示取值可以为多种类型中的一种 使用|
2. 交叉类型 交叉类型是将多个类型合并为一个类型 既是又是 使用& 和 extends 相似 但是 extends 表示继承
3. 类型断言 将类型推断为具体的数值 两种使用方法

```
interface A {
    run: string
}

interface B {
    build: string
}

let fun = (type: A| B):void => {
    <!-- 两种写法 -->
    console.log((type as A).run);
    console.log((<A>type).run)
}
```

4. 类型断言只能够「欺骗」TypeScript 编译器，无法避免运行时的错误，反而滥用类型断言可能会导致运行时错误
5. 可以使用 any 类型做临时断言

## 内置对象

### ecma 对象

new 后面是什么就是什么类型，例如：Number String Boolean Date RegExp Error XMLHttpRequest

```
let a: Number = new Number(100);
let b: String = new String('1');
let c: Boolean = new Boolean(false);
let d: Date = new Date();
let xhr = new XMLHttpRequest();
```

### DOM 元素类型

1. HTML + 元素名称 + Element
2. 无特殊含义标签为 HTMLElement 例如 header footer
3. 获取到数组列表时 使用 NodeList 动态列表使用 NodeListOf< T >

```
let div: HTMLDivElement | null = document.querySelector('div');
let input: HTMLInputElement | null = document.querySelector('input');
let header:HTMLElement | null = document.querySelector('header');
let list:NodeList = document.querySelectorAll('li');
let dynamicList:NodeListOf<HTMLDivElement> = document.querySelectorAll('div li');
```

### BOM 元素类型

1. localStorage/sessionStorage-->Storage
2. cookie-->string
3. location-->Location
4. promise-->Promise< T > T promise 返回值

```
let local:Storage = localStorage;
let session:Storage = sessionStorage;
let lo:Location = location;
let cookie:string = document.cookie;
let promise: Promise<number> = new Promise((resolve: Function, reject: Function) => resolve(1));
```

## Class 类

1. class 的约束 implements 类的继承 extends 必须配合 super()使用
2. class 修饰符 readonly（只读） private （只能自己内部使用） protected（子类和内部使用） public（哪里都可以用）
3. super 原理 super.constructor.call(); 也就是可以通过 super 给父类传参
4. 静态方法 通过类直接调用 例如：Promise.all() static 里面的 this 只能指向其他 static
5. get set 和 object.defineProperty()基本一致 拦截器
6. 基类 抽象类(abstract) 所定义的抽象类 只能继承 不能实例化 所定义的方法 只能进行描述 不能进行具体的实现

```
// 定义参数类型
interface Options {
    el: string | HTMLElement
}

// Vue类数据结构
interface Vuecls {
    options: Options,
    init:()=>void
}

// Dom数据结构
interface VNode {
    tag: string,
    text?: string,
    children?: VNode[]
}

// 实现虚拟dam 简单版本
class Dom {
    constructor (name: string) {
        console.log(name);
    }
    // 创建节点方法
    private createElement(el: string): HTMLElement{
        return document.createElement(el);
    }
    // 添加文本节点
    private setText(el: HTMLElement, text: string):void {
        el.textContent = text;
    }
    // 渲染节点
    protected render(data: VNode):HTMLElement {
        let root:HTMLElement = this.createElement(data.tag);
        if (data.children && Array.isArray(data.children)) {
            data.children.forEach((item:VNode) => {
                let child = this.render(item);
                root.appendChild(child);
            })
        } else {
            this.setText(root, data.text as string);
        }
        return root;
    }
}

// 类的约束 implements
class Vue extends Dom implements Vuecls{
    // 默认方法和参数都在constructor进行接收
    readonly options: Options;
    constructor(options: Options) {
        //  初始化父类方法  也就是初始化Dom类中的createElement setText render
        super('zhang'); // super.constructor.call(); 也就是子类可以通过super向父类传参
        this.options = options;
        this.init();
    }
    static version (): string {
        return '1.0.0';
    }
    public init(): void {
        // 初始化数据
        let data:VNode = {
            tag: 'div',
            text: 'hello world',
            children: [
                {
                    tag: 'option',
                    text: '子节点1'
                },
                {
                    tag: 'option',
                    text: '子节点2'
                }
            ]
        }
        let app = typeof this.options.el == 'string' ? document.querySelector(this.options.el) : this.options.el;
        app?.appendChild(this.render(data));
    }
}
// 静态方法只能类直接调用
let ver = Vue.version();
console.log(ver);

// Vue类的实例化
new Vue({
    el: '#app'
})

// get set 方法演示
class Ref {
    _val: any;
    constructor(value:any) {
        this._val = value;
    }

    get val() {
        return this._val;
    }
    set val(newVal:any) {
        this._val = newVal;
    }
}

const ref = new Ref(1);
ref.val = 2;
console.log(ref.val);

// 抽象类
abstract class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
        console.log('[Animal]', name);

    }
    getName():string {
        console.log('[getName]', this.name);
        return this.name;
    }
    // 抽象方法
    abstract init(name:string):void;
}

// 通过派生类进行继承
class Cat extends Animal {
    constructor() {
        super('cat');
    }
    init(name:string) {
        console.log('[init]',name);
    }
    setName(name: string) {
        this.name = name;
        console.log('[setName]', this.name);
    }
}

let cat = new Cat();
cat.init('init');
cat.setName('1111');
cat.getName();
```

## 元组类型

应用可以定义 excel 返回的数据

```
let arr2:[number, string] = [1, 'a'];
arr2.push(3);
console.log(arr2)
type first = typeof arr2[0];
let num:first = 1;
```

## 枚举类型 enum

1. 普通枚举 递增枚举(不赋值时默认从 0 递增) 自定义枚举 字符串枚举 接口枚举 const 枚举
2. const 枚举声明 和 普通枚举的区别。变量编译后是常量，普通声明编译后是对象。因此，为了避免在额外生成的代码上的开销和额外的非直接对枚举成员的访问，我们可以使用 const 枚举。
3. 反向映射 只能使用数字类型；正向映射（key-->value） 反向映射(value-->key)

```
enum Color {
    Red = 0,
    Green,
    Blue
}
enum Color2 {
    red = 'red',
    green = 'green',
    blue = 'blue'
}
interface A {
    red: Color2.red;
}
let a:A = {
    red: Color2.red,
}

console.log(a.red);
console.log(Color['Red']);

// const 枚举
const enum Types {
    success,
    fail
}

let code:number = 1;
if (code == Types.success) {
    console.log('success');
} else {
    console.log('fail');
}

// 映射

enum MapTypes {
    success,
}

let Num:number = MapTypes.success;
let key = MapTypes[Num];
console.log(`value--${Num}`, `key--${key}`);
```

## 类型别名 type

1. 类型别名用来给一个类型起个新名字，类型别名常用于联合类型
2. type 和 interface 的区别
   1. 类型别名可以作用于原始类型，而接口不行
   2. 两个 type 不能使用 extends 继承, 可以使用 &
   3. extends 在 type 中表示 包含意思 左边是右边类型的子类型

```
type s = string | number;
type s1 = () => void;
type s3 = 1 extends unknown ? 1 : 0;
let params2:s3 = 1;
console.log(params2);
```

## 类型推断

1. ts 自带：在没有明确指定类型时，推测出一个类型
2. 声明变量时直接赋值，但不指明类型，不能再赋值其他类型
3. 声明变量时指明类型，但未赋值，可以赋值其他类型， 推断为 any 类型

```
// 类型推断为number
let guess = 1;

// 声明变量不赋值 类型推断为any
let guess2;
guess2 = 1;
guess2 = 'abc';
```

## never 类型

1. 表示不应该存在的状态
2. 返回 never 的函数必须存在无法达到的终点
3. never 在联合类型中会被直接移除
4. 适用于兜底逻辑

```
// a 为never类型
type a = string & number;

// 返回never的函数必须存在无法达到的终点
// 死循环 没有返回值
let loop = ():never => {
    while(true) {}
}

// 报错，肯定不会有返回值
let throwE = ():never => {
    throw new Error('error');
}

// string | number never被直接移除
type neverParams = number | string | never

type name = 'zhangsan' | 'lisi' | 'wangwu';
let choseName = (value:name) => {
    switch (value) {
        case 'zhangsan':
            console.log('zhangsan');
            break;
        case 'lisi':
            console.log('lisi');
            break;
        case 'wangwu':
            console.log('wangwu');
            break;
        default:
            // 兜底逻辑
            const error: never = value;
            return error;
    }
}
```

## symbol 类型

1. symbol.for for 全局查看 symbol 有没有注册过这个 key，如果有直接用，否则创建
2. 应用场景：解决对象属性重复问题，用作对象的键
3. symbol 无法通过 for...of 遍历得到
4. Object.getOwnPropertySymbols(obj) 只能拿到 symbol 定义的属性 key
5. Reflect.ownKeys(obj) 能拿到 symbol 及普通属性
6. 迭代器的语法糖 for...of
7. 解构底层也是调用[symbol.iterator]
8. 给对象添加 for...of 也就是添加迭代器

```
let a1:symbol = Symbol(1);
let a2:symbol = Symbol(1);

// false
console.log(a1 == a2);

// true
console.log(Symbol.for('a') === Symbol.for('a'))

// { name: 1, [Symbol(1)]: 111, [Symbol(1)]: 222 }
let obj2 = {
    name: 1,
    [a1]: 111,
    [a2]: 222
}

console.log(obj2);

Object.getOwnPropertySymbols(obj2);
Reflect.ownKeys(obj2);

let arr1:number[] = [1,2,3];

// 实现迭代器
let each = (value:any):void => {
    let It:Iterator<any> = value[Symbol.iterator]();
    let next:any = {done: false};
    while (!next.done) {
        next = It.next();
        if (!next.done) {
            console.log(next.value);
        }
    }
}

each(arr1);

for (let value of arr1) {
    console.log(value);
}

let [a,b,c] = [4,5,6];
let arr2:number[] = [4,5,6];
let copy:number[] = [...arr2];
console.log(a,b,c);
console.log(copy);

// 使用for...of遍历对象
let obj = {
    max: 5,
    current: 0,
    [Symbol.iterator]() {
        return {
            max: this.max,
            current: this.current,
            next() {
                if (this.current == this.max) {
                    return {
                        value:undefined,
                        done: true
                    }
                }else {
                    return {
                        value: this.current++,
                        done: false
                    }
                }
            }
        }
    }
}

for (let value of obj) {
    console.log('symbol', value);
}
```
