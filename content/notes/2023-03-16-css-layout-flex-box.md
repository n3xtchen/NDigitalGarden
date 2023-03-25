---
title: "CSS 布局: Flexible Box(FlexBox)"
date: 2023-03-16T22:45:00
tags:
- CSS
---

**FlexBox**[^1] 是由 **W3C** 的 **弹性盒子工作组（Flexbox Working Group）** 提出的，**CSS3** 新增的布局方式，旨在为Web应用程序提供一种更加灵活的==一维布局==解决方案。

### FlexBox 的创立者

**弹性盒子工作组（Flexbox Working Group）** 最初成立于2009年，至2012年成为正式的 **W3C** 工作组。工作组中的成员包括**微软**、**谷歌**、**苹果**等公司，以及多个 **Web** 技术方面的专家。他们致力于推动 **Flexbox** 规范的发展和推广，以便为 **Web** 开发人员提供更加丰富和灵活的布局方式。

### FlexBox 的特点

**FlexBox** 的特点是==可以轻松对齐和拉伸元素==，使元素==在相同的空间内自适应排列==。

**FlexBox** 的优点包括：
1. 灵活性：可以轻松控制布局方式、对齐方式、间距、顺序等。
2. 自适应性：弹性盒子的元素可以==自适应其父容器的宽度和高度==。
3. 容器控制：弹性容器可以设置 `flex-wrap`、`justify-content` 等属性来控制子元素的布局。
4. 容易维护：使用 **FlexBox** 可以使代码结构更加简洁，易于维护。

### FlexBox 的适用场景

1. 动态布局：**FlexBox** 是一种非常灵活的布局方式，可以根据屏幕尺寸和设备类型自动调整元素的大小和位置。因此，**FlexBox** 非常适合需要在不同设备上显示不同内容的动态布局。
2. 导航栏：**FlexBox** 可以轻松实现导航栏的排列，同时还能够控制导航栏的各个元素之间的间距和对齐方式。这种布局方式还可以使导航栏在不同设备上保持良好的显示效果，为用户提供更好的导航体验。
3. 列表布局：**FlexBox** 可以帮助我们轻松实现复杂的列表布局，例如网站中的产品展示、新闻列表等。使用 **FlexBox** 的优势是，我们可以轻松调整列表项之间的间距、对齐方式和排序方式等，从而更好地组织和呈现内容。
4. 响应式布局：响应式布局是一种为不同屏幕尺寸和设备类型设计网站的方法。**FlexBox** 可以帮助我们制作出响应式布局，以便为用户提供更加流畅和符合要求的用户体验。
5. 单行布局：**FlexBox** 也适用于单行的网页布局，例如页头、页脚等。使用**FlexBox** 可以轻松地将元素按照设计要求排列和对齐。另外，**FlexBox** 还可以帮助我们轻松地调整元素的大小和位置，以适应不同设备和屏幕尺寸的要求。

### FlexBox 模型介绍

![](notes/images/47ea8697d5f17a3ba0dfa62e8830270c_MD5.svg)

采用 **FlexBox**  的元素，称为 **Flex 容器**（Flex Container），简称**容器**。它的所有子元素自动成为容器成员，称为 **Flex 元素**（Flex Item），简称**元素**（网络上很多人翻译成**项目**，例如阮一峰的《Flex 布局教程》[^2]，我觉得**元素**更加准确点）。

容器==默认==存在两根轴：水平的**主轴**（main axis）和垂直的**交叉轴**（cross axis）。**主轴**的开始位置（与边框的交叉点）叫做 `main start`，结束位置叫做 `main end`；**交叉轴**的开始位置叫做 `cross start`，结束位置叫做 `cross end`。

**元素**默认沿主轴排列。单个**元素**占据的**主轴**空间叫做 `main size`，占据的**交叉轴**空间叫做 `cross size`。

> 为什么要使用**主轴**/**交叉轴**而不是**横轴**/**纵轴**？
> 
> 在二维图形和计算机图形学中，我们经常使用**横轴**和**纵轴**（也称为 X 轴和 Y 轴）来描述坐标系，其中横轴是==水平方向==上的轴线，纵轴是==垂直方向==上的轴线。
> 
> 而 **FlexBox** 的轴线是相对关系，需要定义**主轴**的方向，而垂直于这个**主轴**方向就是**交叉轴**，当**主轴**的方向方向是水平，那么**主轴**/**交叉轴**和**横轴**/**纵轴**就重合了，而**主轴**方向是任意的，概念上避免混淆，所以就独立出来了一个新概念。

### FlexBox 的使用

```html
<!-- 这个就是 Flex 容器 -->
<div id="my-container" class="flex-container">
	<!-- 下面 #my-container 容器的 3 个 Flex 元素 -->
	<div class="flex-item-1"></div>
	<div class="flex-item-2"></div>
	<div class="flex-item-3"></div>
</div>
```

#### 属性说明

##### Flex 容器属性

故名思义，就是接下里的 **CSS** 属性在**容器**上使用并起作用，而不是**元素**中：

```css
.flex-container {
	flex-direction: "row";  /* 默认值，从左到右  */ 
	flex-wrap: "nowrap";    /* 默认值，不换行 */
	flex-flow: "row nowrap"; /* <flex-direction> || <flex-wrap> */ 

	justify-content: "flex-start"; /* 默认值，元素排列按照主轴方向对齐 */
	align-items: "flex-start";     /* 默认值，元素排列按照交叉轴方向对齐 */

	/* 多个交叉轴的时候，默认值，占满整个交叉轴 */
	align-content: "stretch";   
}
```

- `flex-direction`: 定义**主轴**方向
- `flex-wrap`: 定义**换行**方式 
- `flex-flow`: `flex-direction` 和 `flex-wrap` 组合配置
	- 格式：`<flex-direction> <flex-wrap>`

**定义容器内元素的对齐方式**

- `justify-content`: 定义子**元素**在**主轴**上的对齐方式
	- `flex-start`：左对齐
	- `flex-end`：右对齐
	- `center`： 居中
	- `space-between`：两端对齐，**元素**之间的间隔都相等。
	- `space-around`：每个**元素**两侧的间隔相等，**元素**之间的间隔比**元素**与容器**边框**的间隔大一倍
- `align-item`：定义子**元素**在**交叉轴**上的对齐方式
	- `stretch`：如果**元素**未设置高度或设为auto，将占满整个容器的高度；
	- `flex-start`：交叉轴的起点对齐
	- `flex-end`：交叉轴的终点对齐
	- `center`：交叉轴的中点对齐
	- `baseline`: **元素**的第一行文字的基线对齐
- `align-content`: 当子**元素**在多个交叉轴**上的对齐方式
	- 起效场景：`flex-wrap` 配置成==可以换行的，并且子**元素**确实出现换行情况下==；才能生效
	- `stretch`：轴线占满整个交叉轴
	- `flex-start`：与交叉轴的起点对齐
	- `flex-end`：与交叉轴的终点对齐
	- `center`：与交叉轴的中点对齐
	- `space-between`：与**交叉轴**两端对齐，轴线之间的间隔平均分布
	- `space-around`：每个**元素**两侧的间隔相等，**元素**之间的间隔比**元素**与容器**边框**的间隔大一倍

##### Flex 元素属性
```css
.flex-item-1 {
	order: 0;          /* 默认值，即根据元素出现顺序根据 `justify-content` 对其 */
	flex-grow: 0;      /* 默认值，有多余空间，也不放大*/
	flex-shrink: 1;    /* 默认值，空间不足的时候，等比例缩小*/
	flex-basis: auto;  /* 默认值，元素本来大小 */
	flex: 0 1 auto;    /* <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> */
	align-self: auto;  /* 默认值，服从容器规则 */ 
}
```

- `order`: 元素的排列顺序（整数）
	-  数值越小，排列越靠前
- `flex-grow`：元素的放大方式（整数）
	- `0`: 不放大
	- 如果所有元素都不是 `0`, 元素的放大比例按照设定的数值比例来放大
- `flex-shrink`: 元素的缩小方式（整数）
	- `1`: 空间不足的时候，等比例缩小
	- 如果一个元素的`flex-shrink`属性为0，其他元素都为1，则空间不足时，前者不缩小。
- `flex-basis`：**元素**占据的**主轴空间**（整数）
	- 当它可以设为跟 `width` 或` height` 属性一样的值（比如350px），则**元素**将占据固定空间。
- `flex`: `flex-grow`, `flex-shrink` 和 `flex-basis` 的组合配置
	- 格式：`flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]`
	- 还提供了两个快捷值：
		- `auto` : `1 1 auto`) 
		- `none` : `0 0 auto`)
- `align-self`: **元素**定义**交叉轴**方向对齐方式
	- 属性参考 `align-item`

如果想要具体案例，可以访问（挺完整的）：[flex布局与传统布局对比 - 掘金](https://juejin.cn/post/6968365405257596958#heading-23)，下面是一个 `align-content` 生效的场景


<div style="background-color: blue; width: 150px; height: 150px; margin: 0 auto; display: flex; align-content: space-between;flex-flow: row wrap;">
	<div style="background-color: red; width: 40px; height: 40px;" ></div> 
	<div style="background-color: red; width: 40px; height: 40px;" ></div> 
	<div style="background-color: red; width: 40px; height: 40px;" ></div> 
	<div style="background-color: green; width: 40px; height: 40px;" ></div> 
	<div style="background-color: green; width: 40px; height: 40px;" ></div> 
</div>




[^1]: [CSS Flexible Box Layout Module Level 1](https://www.w3.org/TR/css-flexbox-1/)
[^2]: [Flex 布局教程：语法篇 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)