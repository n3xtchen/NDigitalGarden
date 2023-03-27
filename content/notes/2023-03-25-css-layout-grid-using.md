---
title: "CSS 布局: Grid(网格)的使用"
date: 2023-03-25T18:46:00+08:00
tags:
- CSS
---

### 划分网格

```css
#grid-container {
	display: grid;
	grid-template-columns: 150px 3fr auto;
 	grid-template-rows: 1fr 3fr 1fr;
}
```

- `grid-template-rows`: 行轨道尺寸
- `grid-template-columns`: 列轨道的尺寸
- **网格轨道**的尺寸：
	- `{n} px` / `auto` / `{n} fr` (`n` 是数值)
	- `auto`：根据**网格项**自动控制网格的生成
	- `{n} px`：固定尺寸
	- `minmax(最小, 最大)`

```css
#header {
	grid-row: 1 / 2;        /* [grid-row-start] / [grid-row-end] */
	/*
	grid-row: 1;            // 因为只跨越1行，所以 /2 可省略，所以和上一个例子是等价的
	grid-row-start: 1;      // 行起始网格线
	grid-row-end: 2;        // 行结束网格线，因为只跨越1行，所以，这个配置可以省略
	*/
	
	grid-column: 1 / 4;     /*  [grid-column-start] / [grid-column-end] */
	/*
	grid-column-start: 1;   // 列起始网格线
    grid-column-end: 4;     // 列结束A网格线
    */
}
```

- `grid-[row|column]` 是 `grid-[row|column]-start` 和 `grid-[row|column]-end` 的简写，两个值需要用 `/` 隔开;
- **网格线**的==编号从1 开始==;
- **网格项**==只跨越一**行**或**列**，则`grid-row-end`/`grid-column-end`的定义可以省略==;
- `span` 用来指定跨越**行**或**列**的数量
	- `span 2`: 等价于 `[起始网格线编号] + 2`
	- 所以上面的例子，我们可以重写为：
		- `grid-column: 1 / span 3`
		- ==使用 `span` 时，如果 `[起始网格线编号]` 是 1 的话，可以省略==；
		- 所以可以继续改写：
			- `grid-column: span 3`

### 命名网格线

上面的代码，**网格线**编号是隐藏，为了演示，我可以进行如下该下：

```css
#grid-container {
	display: grid;
	grid-template-columns: [1] 150px [2] 3fr [3] auto [4];
 	grid-template-rows: [1] 1fr [2] 3fr [3] 1fr [4];
}
```

为了==增加代码的可读性==，我们可以为这些编号起一个有意义的名称，我为 `#header` 命名了**网格线**：

```css
#grid-container {
	display: grid;
	grid-template-columns: 150px 3fr auto;
 	grid-template-rows: [header-start] 1fr [header-end] 3fr 1fr;
}

#header {
	grid-row: header-start / header-end;  /* 是不是可读性增强了？ */
	grid-column: 1 / 4;
}
```

由于**网格布局**是2维的，可能同样一个**行** **网格线** 编号，在不同**列**的含义可能不同（同样适用于**列** **网格线**编号），所以我们可以一条**网格线**取多个不同名称：

```css
#grid-container {
	display: grid;
	grid-template-columns: [body-start main-start] 150px 3fr [main-end] auto [body-end];
	/* 不同的命名针对不同的情况 */
 	grid-template-rows: [header-start] 1fr [main-start] 3fr [footer-start] 1fr;
}

#header {
	grid-row: header-start;
	grid-column: body-start / body-end; /* header 需要占用所有类 */
}

#main {
	grid-row: main-start;
	grid-column: main-start / main-end;  /* 左侧主题，他占据头两列 */
}
```

### `repeat` 和同名网格线

如果我想要定义一个任务管理界面，有四栏列表，列表的宽度和列表间的间隔都一致：

```css
#task-container {
	display: grid;
	grid-template-columns: repeat(4, 10px [task-list-start] 1fr [task-list-end]) 10px;
	/*
	等价于
	grid-template-columns: 10px 1fr 10px 1fr 10px 1fr 10px;
	代码显得简洁了很多吧
	*/
}

/* 待办 */
#todo {
	grid-column: task-list-start / task-list-end;
	/*
	grid-column：2 / 3; 
	*/
}

/* 正在做 */
#doing {
	grid-column: task-list-start 2 / task-list-end 2;
	/*
	grid-column：4 / 5; 
	*/
}

/* 已完成 */
#done {
	grid-column: task-list-start 3 / task-list-end 3;
	/*
	grid-column：6 / 7; 
	*/
}

/* 已归档 */
#archive {
	grid-column: task-list-start 4 / task-list-end 4;
	/*
	grid-column：8 / 9; 
	*/
}
```

- 根据重复出现次数：`[同网格线名] [重复出现次数+1]`；
	- 第二次：2，第三次：3，以此类推；
- `repeat` 带来的==简洁度==应该是很明显；
- **网格项**的成对**网格线**名称定义应该比数字编号看起来，友好很多吧，==可读性也加强==了；

### 使用网格区域

```css
#grid-container { 
	display: grid; 
	grid-template-rows: 150px 1fr 100px; 
	grid-template-columns: 1fr 200px; 
}

#sidebar {
	grid-area: 2 / 2 / 3 / 3;
}
```

- `grid-area: [grid-row-start] / [grid-column-start] / [grid-row-end] / [grid-column-end]` ；
- 如果省略 `grid-column-end`，则如果 `grid-column-start` 是自定义标识符，则 `grid-column-end` 设置为该标识符；否则，它将被设置为 `auto`；
- 如果省略 `grid-row-end`，则如果 `grid-row-start` 是自定义标识符，则 `grid-row-end `设置为该自定义标识符；否则，它将被设置为 `auto`；
- 如果省略 `grid-column-start`，则如果 `grid-row-start `是自定义标识符，则所有四个longhand属性都将被设置为该值。否则，它将被设置为 `auto`；

下面是**网格区域**的另一种定义方式

```css
#grid-container { 
	display: grid; 
	/* 是不是视觉感十足 */
	grid-template-areas: "header header" 
	                     "content sidebar" 
                         "footer footer"; 
	grid-template-rows: 150px 1fr 100px; 
	grid-template-columns: 1fr 200px; 
}

#sidebar {
	grid-area: sidebar;
}
```

- ==网格区域之间是不可重叠的==；