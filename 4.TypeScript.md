# TypeScript

## 基础类型

1. number string boolean void 等
2. void 表示没有函数返回值为空
3. 也可以将 undefined 和 null(非严格模式下) 赋值给 void

## 类型顺序

1. 顶层类型 unknown any
2. object
3. Number String Boolean
4. number string boolean
5. 123 6. never

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
2. 交叉类型 交叉类型是将多个类型合并为一个类型 使用& 和 extends 相似 但是 extends 表示继承
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
```
