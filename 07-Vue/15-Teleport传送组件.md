# Teleport

Teleport 能够将我们的模板渲染到指定 DOM 节点，不受父级 style, v-show 等属性影响，但是 data, prop 数据依旧能够共用的技术

主要解决的问题 Teleport 节点挂载在其他指定的 DOM 节点下，完全不受父级 style 样式影响

## 使用方法

通过 to 属性 插入指定元素位置 to="body" 便可以将 Teleport 的内容传送到指定位置

```html
<!-- 在父级组件中，将Loading组件传送至body，不受当前组件style影响 -->
<Teleport to="body">
  <Loading></Loading>
</Teleport>
```

## 动态控制 teleport

使用 disabled 设置为 true 则 to 属性不生效 false 则生效

```html
<teleport :disabled="true" to="body">
  <a></a>
</teleport>
```
