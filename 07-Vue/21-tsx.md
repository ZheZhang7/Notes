# tsx

## 安装插件

```shell
npm install @vitejs/plugin-vue-jsx -D
```

## vite.config.ts 配置

```ts
// 其余代码
import vueJsx from "@vitejs/plugin-vue-jsx";
export default defineConfig({
  plugins: [vue(), vueJsx()],
});
```

## 修改 tsconfig.json 配置文件

```shell
"jsx": "preserve",
"jsxFactory": "h",
"jsxFragmentFactory": "Fragment",
```

## 使用 tsx

- tsx 支持 v-model 使用

  tsx 不会自动解包 使用 ref 需要加上.value

```tsx
import { ref } from "vue";
let v = ref<string>("");

const renderDom = () => {
  return (
    <>
      <input type="text" v-model={v.value} />
      <p>{v.value}</p>
    </>
  );
};

export default renderDom;
```

- v-show 正常使用

```tsx
import { ref } from "vue";
let flag = ref(false);

const renderDom = () => {
  return (
    <>
      <div v-show={flag.value}>1</div>
      <div v-show={!flag.value}>2</div>
    </>
  );
};

export default renderDom;
```

- v-if 无法支持 使用 js 写法替代（三目运算）

```tsx
import { ref } from "vue";
let flag = ref(false);

const renderDom = () => {
  return <>{flag.value ? <div>1</div> : <div>2</div>}</>;
};

export default renderDom;
```

- v-for 不支持，使用 map 代替

```tsx
import { ref } from "vue";

const arr = [1, 2, 3, 4, 5];

const renderDom = () => {
  return (
    <>
      {arr.map((item) => {
        return <div>${item}</div>;
      })}
    </>
  );
};

export default renderDom;
```

- v-bind 支持

```tsx
import { ref } from "vue";

let arr = [1, 2, 3, 4, 5];

const renderDom = () => {
  return (
    <>
      <div data-arr={arr}>1</div>
    </>
  );
};

export default renderDom;
```

- v-on 绑定事件 react 风格

  所有事件用 on 开头，所有事件名称首字母大写

```tsx
const renderDom = () => {
    return (
        <>
            <button onClick={clickTap}}>点击</button>
        </>
    )
}

const clickTap = () => {
    console.log('点击事件')
}

export default renderDom;
```

- Emit 派发

```tsx
type Props = {
  title: string;
};

const renderDom = (props: Props, content: any) => {
  return (
    <>
      <div>{props.title}</div>
      <button onClick={clickTap.bind(this, content)}>点击</button>
    </>
  );
};

const clickTap = (ctx: any) => {
  ctx.emit("on-click", 1);
};
```

- Slot

```tsx
const A = (props, { slots }) => (
  <>
    <h1>{slots.default ? slots.default() : "foo"}</h1>
    <h2>{slots.bar?.()}</h2>
  </>
);

const App = {
  setup() {
    const slots = {
      bar: () => <span>B</span>,
    };
    return () => (
      <A v-slots={slots}>
        <div>A</div>
      </A>
    );
  },
};

// or

const App = {
  setup() {
    const slots = {
      default: () => <div>A</div>,
      bar: () => <span>B</span>,
    };
    return () => <A v-slots={slots} />;
  },
};

// or you can use object slots when `enableObjectSlots` is not false.
const App = {
  setup() {
    return () => (
      <>
        <A>
          {{
            default: () => <div>A</div>,
            bar: () => <span>B</span>,
          }}
        </A>
        <B>{() => "foo"}</B>
      </>
    );
  },
};
```

## 实现一个 vite 插件解析 tsx

```shell
npm install @vue/babel-plugin-jsx
npm install @babel/core
npm install @babel/plugin-transform-typescript
npm install @babel/plugin-syntax-import-meta
npm install @types/babel__core
```

```ts
import type { Plugin } from "vite";
import * as babel from "@babel/core"; //@babel/core核心功能：将源代码转成目标代码。
import jsx from "@vue/babel-plugin-jsx"; //Vue给babel写的插件支持tsx v-model等
export default function (): Plugin {
  return {
    name: "vite-plugin-tsx",
    config(config) {
      return {
        esbuild: {
          include: /\.ts$/,
        },
      };
    },
    async transform(code, id) {
      if (/.tsx$/.test(id)) {
        //@ts-ignore
        const ts = await import("@babel/plugin-transform-typescript").then(
          (r) => r.default
        );
        const res = babel.transformSync(code, {
          plugins: [jsx, [ts, { isTSX: true, allowExtensions: true }]], //添加babel插件
          ast: true, // ast: 抽象语法树，源代码语法结构的一种抽象表示。babel内部就是通过操纵ast做到语法转换。
          babelrc: false, //.babelrc.json
          configFile: false, //默认搜索默认babel.config.json文件
        });
        return res?.code; //code: 编译后的代码
      }

      return code;
    },
  };
}
```
