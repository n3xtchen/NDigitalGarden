---
title: "CSS 布局: Grid(网格)"
date: 2023-03-21T21:30:00+08:00
tags:
- CSS
---

**Grid 布局**[^1] 由 W3C 前 **CSS** 工作组主席 Bert Bos 和谷歌工程师 Tab Atkins Jr. 共同设计和开发。该规范于 2017 年 6 月 6 日成为官方 **W3C** 推荐标准，并且已成为现代 **Web** 开发中广泛使用的布局模型之一。**Grid 布局**的设计理念是提供一种灵活、强大、直观的方式来实现多种精美页面布局。它提供了一种==二维网格==来定义和控制页面上的工作区域，以及一个简单易用的 **API** 来管理和布置网格项。

### Grid 布局的特点

**Grid 布局** 具有非常强大、灵活、易于维护和自适应等特点，非常适合用于开发复杂的网格布局。

1. 二维布局：可以在水平方向和垂直方向上同时处理布局。
2. 分割轨道：允许将轨道分割为具有相同或不同大小的网格单元格，从而创建复杂的布局。
3. 明确布局：开发人员可以使用明确的语法和属性来定义各个元素的位置和大小。
4. 自适应布局：具有自适应的特点，可以根据设备大小和方向动态地调整布局。
5. 灵活性：具有非常大的灵活性，可以处理各种情况，包括多列、多行、等高度和非等高度的网格。
6. 重叠单元格：允许网格单元格重叠，从而可以创建更复杂的布局。

### Grid 布局的适用场景

1. 多列布局：使用**Grid 布局**创建多列布局非常容易，可以定位和对齐列，从而在网格和剩余空间之间创建新的缩放和滚动上下文。
2. 多行布局：**Grid 布局**的行轨道和列轨道都可以设置空间，可以通过使用无数线实现多行布局，无论是网格或其他非结构化元素都可以嵌套在行中。
3. 水平和垂直居中：使用 Grid 布局可以非常容易地将元素居中，只需要将它们发布到网格的中心即可。这还可以方便地实现垂直对齐。
4. 自适应布局：使用**Grid 布局**可以让元素自由地在多个屏幕尺寸上自适应，这使得开发 Responsive Layout 等越来越受欢迎。

#### 能够取代表格布局和多列布局吗？

在过去，**表格布局**和**多列布局**被广泛用于创建复杂的网格式页面布局，但它们都有一些缺点。表格布局的缺点包括难以使单元格大小相等，以及单元格过度嵌套的风险；多列布局仅适用于单行内容，无法有效地处理多行内容。

### Grid 布局的模型介绍

![Grid lines](https://www.w3.org/TR/css-grid-1/images/grid-lines.png)

在**Grid 布局**中，**网格容器（Grid Container）** 的内容==通过定位和对齐==放置在网格中。**Grid** 是一组相交的水平和垂直**网格线（Grid Lines）**，将**网格容器**的空间分成**网格区域（Grid Areas）**，**网格项（Grid Items）**（代表**网格容器**的内容）可以放置在其中。**网格线**分为两组：一组定义沿块轴运行的**列（Columns）**，另一组定义沿内联轴运行的**行（Rows）**。[^2]

上图的的 **CSS** 的代码实现如下：

```css
#grid {
	display: grid;
	grid-template-columns: 150px 1fr;
	grid-template-rows: 50px 1fr 50px;
}
```

**网格轨道（Grid Track）** 是指**列**或**行**的通用术语（就是某一行或者某一列），换句话说，它是两个相邻**网格线**之间的空间。每个**网格轨道**分配了一个**尺寸函数**，该函数控制该列或行可以增长多宽或多高，从而控制其边界**网格线**之间的距离。

**网格单元格（Grid Cell）** 是网格行和网格列的交点，是网格中可以在定位**网格项**时引用的**最小单位**。

**网格区域（Grid Area）** 是用于布置一个或多个**网格项**的逻辑空间。一个**网格区域**由一个或多个==相邻==的**网格单元**组成。它由==四条网格线约束==，**网格区域**的每一侧各一条，并参与其相交的**网格轨道**的大小调整。**网格区域**可以使用**网格容器**的 `grid-template-areas` 属性显式命名，或通过其边界**网格线**隐式引用。使用 `grid-placement` 属性将**网格项**分配给**网格区域**。

> **网格区域**和**网格项**的区别？以及如何使用呢？
> 
> **网格区域间**是==不可相互重叠==的，而且它是逻辑空间而不是实际空间；而**网格项**代表**网格容器**的具体内容，不同的**网格项**可以==相互重叠==（所以==多个**网格项**可以占用同一个**网格区域**==，而且分配同一个**网格区域**的**网格项**之间不会相互影响，可以看一下官方的例子[^3]）。
> 
> 如果你的布局要求是需要将若干个**网格单元格**组合在一起，形成一个大的区域，然后将元素放置在这个大的区域中，那么==使用网格区域可能更加方便==。
>
> 如果你的布局要求是需要在整个**网格容器**中==灵活地布局多个元素==，并且它们的尺寸和位置也没有明显的规律，那么==使用网格项更加方便==。

### fr：可变长度的单位

我们先看一个例子，`grid-template-columns: 150px 1fr 1fr;` 说明将声明一个有 3 列的**网格布局**：
- 第一列的宽度是固定的，永远都是 `150px`；
- 第2和3列的 `width` 是可伸缩的， 值是$$\frac{(width_{网格容器}-150px)}{2}$$。正是这个机制，让**网格布局**具有的==自适应==的能力。在这个

`fr`，**fraction** 的缩写，故名思义，**分数**，**网格容器**根据**分数**自动伸缩长度。某一列/行的份额计算方式：$$剩余空间 *\frac{fr_{某一行或列}}{\sum^{i=1}_{i<={最大行或列数}}{fr_i}}$$
将行/列上，将不可伸缩的行/列的宽度固定（就是例子中的第一列），对于可伸缩的行/列（即单位是 `fr`，就是例子中第 2 和 3 列）根据份额百分比进行伸缩。

### 网格布局的使用

假设我需要将第一行作为 `header`，最后一行作为 `footer`，中间左边是 `sidebar` 以及中间右边作为 `main`：

```css
#header {
	background-color: blue;
	grid-row: 1;
	grid-column: 1 / span 2;
}

#sidebar {
	background-color: yellow; 
	grid-row: 2;
	grid-column: 1;
}

#main {
	background-color: green; 
	grid-row: 2;
	grid-column: 2;
}

#footer {
	background-color: grey; 
	grid-row: 3;
	grid-column: 1 / span 2;
}
```


<div style="background-color: blue; width: 200px; height: 200px; margin: 0 auto; display: grid; grid-template-columns: 50px 1fr; grid-template-rows: 30px 1fr 30px;">
	<div style="background-color: blue; grid-row: 1;grid-column: 1 / span 2;" ></div> 
	<div style="background-color: yellow; grid-row: 2;grid-column: 1;" ></div> 
	<div style="background-color: green; grid-row: 2;grid-column: 2;" ></div> 
	<div style="background-color: grey; grid-row: 3;grid-column: 1 / 3; " ></div> 
</div>



[^1]: [CSS Grid Layout Module Level 1](https://www.w3.org/TR/css-grid-1/)
[^2]: [CSS Grid Layout Module Level 1-Grid Concept](https://www.w3.org/TR/css-grid-1/#grid-concepts)
[^3]: [CSS Grid Layout Module Level 1](https://www.w3.org/TR/css-grid-1/#grid-area-concept)