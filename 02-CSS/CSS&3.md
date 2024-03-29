# CSS & 3

- [权重](#权重)
- [不可继承属性](#不可继承属性)
- [可继承属性](#可继承属性)
- [隐藏元素方法](#隐藏元素方法)
- [盒模型](#盒模型)
- [使用 `translate` 和 `position:absolute` 移动元素区别](#使用-translate-和-position:absolute-移动元素区别)
- [常见布局](#常见布局)
- [flex 详解](#flex-详解)
- [浮动](#浮动)
- [BFC](#BFC)
- [BEM 架构](#BEM-架构)
- [Css 原子化](#css-原子化)

## 权重

内连样式 1000 > id 100 > class | 伪类 ｜ 属性 10 > 标签 ｜ 伪元素 1 > 子代 ｜ 兄弟 ｜ \*

## 不可继承属性

1. display
2. 文本（vertical-align text-shadow white-space）
3. 盒子模型（width height margin border padding)
4. 背景属性（background background-color background-image background-repeat background-position background-attachment）
5. 定位属性 (float、clear、position、top、right、bottom、left)

## 可继承属性

1. 字体属性： font-size font-family font-weight font-style
2. 文本属性：text-align text-indent line-height word-spacing color
3. 元素可见性：visibility
4. 光标属性： cursor

## 隐藏元素方法

1. display: none 不占位置 不响应事件
2. visibility: hidden 占位置 不响应事件
3. opacity: 0 占位置 响应事件
4. z-index: -1 用其他元素遮挡
5. position: absolute 移除可视区域
6. transform: scale(0,0) 占位置 不响应事件

## 盒模型

1. 标准盒模型（box-sizing：content-box）： content = width/height
2. ie 盒模型（box-sizing：border-box）：content + padding + border = width/height

## 使用 translate 和 position:absolute 移动元素区别

参考链接：[整体原因](https://blog.csdn.net/thewar196/article/details/124602244)
[浏览器渲染流程](https://blog.csdn.net/duola8789/article/details/79237553)<br />
**浏览器渲染流程**:

1. html-->dom tree
2. css-->style tree
3. dom + style-->render tree
4. render tree layout
5. 合成图层(利用 gpu 渲染 layout)

**硬件加速原理**：<br />
每个渲染元素会被分配到一个图层中，每个图层加载到 gpu 形成如渲染纹理，使用合成线程进行渲染，不会触发 repaint<br />

**3d 和 2d translate 区别**：<br />
3d 在动画渲染前创建独立的复合图层，2d 在运行期间创建；因此 2d 多两次 repaint（创建+删除）

**gpu 加速缺点**：

1. 大量使用 gpu 加速，会渲染大量纹理，导致内存问题
2. 影响字体的抗锯齿效果，硬件加速停止，文本还在动画期导致

**总结**:

1. translate 使用 gpu 加速，性能更好；absolute 使用 cpu，导致 repaint，性能差
2. translate 整个动画过程占据空间，absolute 整个动画过程不占据空间
3. translate 2d 比 3d 多两次 repaint
4. 注意使用 gpu 加速的缺点
5. 理解强制触发硬件加速技巧（例如 rotate 360deg）
6. 触发 gpu 加速的 css 属性：transform opacity filter

## 常见布局

[两栏布局](https://juejin.cn/post/6905539198107942919?searchId=20230729213627280540CD550DBF9D7052#heading-36)
[三栏布局](https://juejin.cn/post/6905539198107942919?searchId=20230729213627280540CD550DBF9D7052#heading-37)
[水平居中](https://blog.csdn.net/qq_53985958/article/details/125510763)
[水平垂直居中](https://juejin.cn/post/6905539198107942919?searchId=20230729213627280540CD550DBF9D7052#heading-38)

## flex 详解

[参考链接](https://blog.csdn.net/wwwjjjjj666/article/details/128033184)<br />

**容器属性**：

1. flex-direction: 主轴方向 row / column
2. flex-wrap: 换行
3. flex-flow: flex-direction + flex-wrap 简写
4. justify-content: 主轴上项目对齐方式 flex-start/end center space-between/around
5. align-items: 交叉轴对齐方式 flex-start/end center baseline(第一行文字基线对齐) stretch(默认，每一项上没设置高度，自动填满父元素)
6. align-content: 多根主轴对齐方式

**项目属性**：

1. flex-grow: 每一项放大比例， 默认 0
2. flex-shrink： 每一项缩小比例，默认 1，空间不足，项目缩小； 0 不收缩<br />
   注意：flex-shrink > 1 当子元素宽度和大于父元素宽度时，flex-shrink 将会按照一定的比例进行收缩，即将子元素宽度之和与父元素宽度的差值按照子元素 flex-shrink 值来分配给各个子元素，指定各个子元素收缩多少，每个子元素原本宽度减去按比例分配的值，其剩余值为收缩完的实际宽度。<br />
   flex-shrink < 1 只收缩溢出空间部分
3. flex-basis: 分配多余空间之前，项目占据的主轴空间； 默认 auto，项目本身大小 干掉 width
4. flex: 1 flex-grow/shrink/basis 1/1/0 自适应

## 浮动

浮动脱离文档流，导致高度塌陷<br />
与浮动元素同级的非浮动元素会跟随其后<br />
父元素的高度无法被撑开，影响与父元素同级的元素<br />

**清除浮动带来的影响**：

1. 给父级 height
2. 给最后一个浮动的元素添加空 div，并且增加 clear：both
3. 给包含浮动的父级元素包含 overflow：hidden
4. 使用 after 伪元素：.clear::after{ content:''; display: block; clear:both;}

## BFC

块级格式化上下文，css 布局中生成的盒子区域

**创建 bfc**：

1. body
2. position：absolute/fixed
3. float: none 以外的值
4. overflow：hidden/auto/scroll

**作用**：

1. 解决 margin 塌陷（将两个元素变成两个 bfc， bfc 独立渲染互不影响）
2. 解决高度塌陷

## BEM 架构

## Css 原子化
