# keep-alive

组件不被重复渲染影响使用体验，或者性能考虑，避免多次重复渲染降低性能。

## 开启 keep-alive 生命周期的变化

- 初次进入时： onMounted--> onActivated
- 退出后触发： onDeactivated
- 再次进入时： 只会触发 onActivated

事件挂载的方法等，只执行一次的放在 onMounted 中；组件每次进去执行的方法放在 onActivated 中

```html
<!-- 基本用法 -->
<keep-alive>
  <comonent :is="view" />
</keep-alive>

<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>
```

## include exclude max

- 传入缓存的组件
- 传入不需要缓存的组件
- 传入最大缓存数 超过数量 使用 LRU 算法移除缓存

```html
<keep-alive :include="[组件名]" :exclude="[组件名]" :max="10">
  <comonent :is="view" />
</keep-alive>
```
