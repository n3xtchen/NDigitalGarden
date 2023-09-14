---
title: "Vim: 缩进"
date: 2023-08-19T17:05:00+08:00
tags:
- Vim
---

### Vim 的自动缩进方式

| 缩进方式             | 官方文档                                         | 说明                                                                                                                                                   |
| -------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 自动缩进             | `:help autoindent`                               | 起新行的时候复制当前的缩进形式；                                                                                                                       |
| 智能缩进             | `:help smartindent`                              | 在 `autoindent` 基础上添加一些缩进规则；<br/>能识别一些 **C** 语法，合适的时候进行自动增减缩进；                                                            |
| **C语言**缩进 | `:help cindent`                                  | 能够分析上下文，提供 **C** 相似语言的自动增减缩进能力；                       |
| 缩进表达式       | `:help indentexpr`  | 自定义自动缩进的方式；<br/>完全自定义比较高阶，就不过多介绍了； |

### Vim 的自动缩进配置方式

| 缩进方式   | 开启的选项     | 配置                 | 生效优先级                     |
| ---------- | -------------- | -------------------- | ------------------------------ |
| 自动缩进   |                | `set autoindent`     | 最后                           |
| 智能缩进   | `+smartindent` | `set smartindent`    | 第三                           |
| **C语言**缩进  | `+cindnet`     | `set cindent`        | 其次                           |
| 缩进表达式 |                | `set indentexpr=`    | 最高                           |
| 文件类型缩进           |                | `filetype indent on` | 它是上述四种的组合，看实际实现 |


> **Vim** 提供了哪些文件类型的智能缩进表达式？
> 你可以在 `Normal` 模式下，键入 `echo $VIMRUNTIME`，获取 **Vim** 的 **Runtime** 目录，在 `${VIMRUNTIME}/indent` 目录下就可以看到 **Vim** 支持的文件类型。

### Vim 下常用的缩进快捷键

| 操作       | 所在的模式        | 产生的效果                              |
| ---------- | ----------------- | --------------------------------------- |
| `Enter`    | `INSERT`          | 下一行自动增减缩进                          |
| `O`/`o`    | `Normal`          | 下一行自动增减缩进                              |
| `>>`       | `Normal`/`Visual` | 增加**当前行/选中行**缩进               |
| `<<`       | `Normal`/`Visual` | 减少**当前行/选中行**缩进               |
| `Ctrl`+`T` | `Insert`          | 增加当前行缩进                          |
| `Ctrl`+`D` | `Insert`          | 减少当前行缩进                          |
| `==`       | `Normal`/`Visual` | 根据上下文，对**当前行/选中行**自动缩进 |
| `gg=G`     | `Normal`          | 自动缩进整个文档                        |
| `=a{`      | `Normal`          | 自定缩进当前所在的代码块，即花括号之间  |

### 我对 Vim 缩进的推荐配置

`.vimrc` 中需要开启的配置：

```vim
...

" 兼容模式下，不能使用 setlocal
" setlocal 可以保证不同文件类型的配置隔离
set nocompatible

" 缩进配置
set autoindent        " 针对普通文本，作为缩进的补充
" Vim 需要编译时引入的功能 `+cindnet`（可以使用 `:version` 中查看），因为有些语言依赖
" 不需要再配置中直接 `+cindnet`，filetype 会根据情况自行引入
filetype indent on    " 打开文件检测，并识别和匹配缩进方式

...
```

验证配置是否生效，`detection` 和 `indent` 需要打开状态，前者识别文件类型，后者告诉 **Vim** 根据识别的文件类型需要加载缩进配置：

```
:filetype
filetype detection:ON  plugin:ON  indent:ON
```


> 不推荐 `smartindent` ，诞生之初，就是 `autoindent` 的更智能版本的存在，但是在他诞生的同期，大部份语言都有自己的缩进表达式或者基于 `cindent` 定制自己的规则， 所以 `smartindent` 就显得鸡肋了。^[1]



[1]: [vi - autoindent is subset of smartindent in vim? - Stack Overflow](https://stackoverflow.com/questions/18415492/autoindent-is-subset-of-smartindent-in-vim)




