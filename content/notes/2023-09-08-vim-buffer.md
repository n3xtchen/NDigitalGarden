---
title: "Vim: Buffer(缓冲)、Window(窗口)和 Tab(标签)"
date: 2023-09-08T21:16:00+08:00
tags:
  - Vim
---
**Buffer**(缓冲区，后面统一使用 **Buffer**) 作为 **Vim** 的一个重要概念，我们先通过官方的帮助文档(`:help window`)中的定义来了解他 

```
Summary:
     A buffer is the in-memory text of a file.
     A window is a viewport on a buffer.
     A tab page is a collection of windows.
```

**Buffer** 是 **Vim** 占用系统一块内存区域，用来存取从文件读取到内容；**Window**(窗口，后面统一使用 **Window**) 是 **Buffer** 的可视区域(Viewport)；**TabPage** (标签页，后面统一使用 **Tab**，帮助文档：`:help tab-page`) 是 **Window** 的集合。 

一个文件==只能有一个== **Buffer**，保证编辑不冲突；一个 **Buffer** 可以被多个 **Window** 展示，在这种情况，编辑一个 **Buffer** 会同步更新它对应的多个 **Window**，保证内容的一致性。

一个 **Buffer** 将文件载入内存中，用于后续查看和编辑。原始文件直到你执行 **Buffer** 写操作(`:w`)的时才会发生变更。

### Buffer 的操作

```
:buffers   " 查看当前所有 Buffer，等价命令：:files/:ls
  1 #    "Makefile"                     line 1
  3  a   "config.toml"                  line 2
  6  h   ".drone.yml"                   line 2
  9 %a   "content/notes/2023-04-10-fewshot-learning.md" line 7
```

解读下上面命令的含义：
- 第一列：**Buffer** 编号
- 第二列：**Buffer** 状态
	-  `#` 非活动的缓冲区
	- `a` 当前被激活缓冲区（光标所在的缓冲区）
	- `h` 隐藏的缓冲区
	- `%` 当前的缓冲区（指当前窗口可见的缓冲区，因为可以分割窗口，可能有多个）
	- `#` 交换缓冲区
	- `=` 只读缓冲区
	- `+` 已经更改的缓冲区
- 第三列：**Buffer** 名称(`bufname('%')`)
- 第四列：该 **Buffer** 区上次编辑的位置

#### 增加/删除/卸载 Buffer

| 快捷键                            | 功能                                                                                   |
| --------------------------------- | -------------------------------------------------------------------------------------- |
| `:b[add] {文件名}`                | 将该文件加到 **Buffer** 列表中，但不会加载它                                           |
| `:bdelete {编号或bufname} ...`    | 删除对应编号或bufname的 **Buffer**，<br/>不是真的删除，而是添加到 `unlisted-buffer` 中 |
| `:bw[ideout] {编号或bufname} ...` | 删除对应编号或bufname的 **Buffer**，<br/>占用的内存页也会完全释放                           |
| `:bunload {编号或bufname} ...`    | 清空对应编号或bufname的 **Buffer** 的内容，<br/>但是 **Buffer** 不会被删除                                                                                      |

#### 编辑 Buffer

| 快捷键                  | 功能                                       |
| ----------------------- | ------------------------------------------ |
| `:[s]b {编号或bufname}` | 在当前窗口编辑{编号或bufname}的 **Buffer** |
| `:[s]bn[ext]`           | 在当前窗口编辑下一个 **Buffer**            |
| `:[s]bp[revious]`       | 在当前窗口编辑上一个 **Buffer**            |
| `:[s]bfirst`/`:blast    | 在当前窗口编辑第一个/最后一个 **Buffer**   |

> `[s]` 表示将会创建一个窗口，编辑相应的 **Buffer**

### TabPage 和 Window 的布局

```
  [No Name]**  2 [No Name]**                                                     
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
~                                        |~
[No Name]               [ ]   1:  0/  1  [No Name]             [ ]   1:  0/  1
:
```

- 上图中是打开了2个标签，当前标签页有2个打开的窗口
- 第一行是 `tabline`(标签栏): 
	- 可以通过 `set tabline=...` 来自定义显示内容
	- 打开标签页的时候，才会显示
- 倒二行是 `statusline`(状态栏):
	- 显示的方式：
		- `set laststatus=0`: 不显示
		- `set laststatus=1`: 只有多窗口的时候才会显示
		- `set laststatus=2`: 每一个窗口都会显示
	- 可以通过 `set statusline=...` 来自定义显示内容

### 查看你当前的 Buffer、Window 和 Tab

- `windo`: 遍历所有窗口
- `winnr()`: 当前窗口编号
- `bufnr()`: 当前缓冲区编号
- `bufname('%')`: 当前缓冲区缓存的文件名
- `tabdo`: 遍历所有窗口
- `tabpagebuflist()`: 当前标签页的缓冲区列表
- `tabpagenr()`: 当前标签页的编码
- `tabpagenr('$')`: 标签页数量

查看窗口编码、窗口中展示缓冲编号和名称：

```
$ touch Makefile README.md
$ ls
Output: Makefile README.md

# 打开空白文件
$ vim
:let x=[]
:windo call add(x, [winnr(), bufnr(), bufname('%')])
:echo x
Output: [[1, 1, '']]

# 打开一个文件
$ vim README.md
:let x=[]
:windo call add(x, [winnr(), bufnr(), bufname('%')])
:echo x
[[1, 1, 'README.md']]

# 打开多个文件
$ vim -o Makefile README.md
:let x=[]
:windo call add(x, [winnr(), bufnr(), bufname('%')])
:echo x
[[1, 1, 'Makefile'], [2, 2, 'README.md']]
```

查看所有标签页码，窗口编码、窗口中展示缓冲编号和名称：

```
:tabdo windo echo [tabpagenr(), winnr(), bufnr(), bufname('%')]
[1, 1, 7, '/opt/miniconda3/envs/develop/share/vim/vim90/doc/tabpage.txt']
[1, 2, 2, 'NetrwTreeListing']
[1, 3, 1, '']
[2, 1, 5, '/opt/miniconda3/envs/develop/share/vim/vim90/doc/windows.txt']
[2, 2, 8, '']
```

### Window 操作

| 快捷键                   | 功能                                                                                                     |
| ------------------------ | -------------------------------------------------------------------------------------------------------- |
| `:sp {filename}`         | 在当前窗口下方创建窗口并打开文件                                                                         |
| `:vs {filename}`         | 在当前窗口右边创建窗口并打开文件                                                                         |
| `:new`                   | 在当前窗口下方创建窗口并创建文件                                                                         |
| `:vnew`                  | 在当前窗口右边创建窗口并创建文件                                                                         |
| `:hide`                  | 隐藏当前窗口                                                                                             | 
| `Ctrl-W` `h`/`j`/`k`/`l` | 上/下/左/右跳转                                                                                          |
| `Ctrl-W` `=`             | 窗口等分，除了固定宽度外                                                                                 |
| `Ctrl-W` `+`/`-`         | 窗口上/下移动                                                                                            |
| `Ctrl-W` `<`/`>`         | 窗口左/右移动                                                                                            |
| `Ctrl-W` `H`/`I`/`J`/`K` | 移动窗口到上/下/左/右                                                                                    |
| `Ctrl-W` `t`/`b`         | 如果是水平分割，最左边，最右边，如果垂直分割，最上面最下面<br/> 混合切割的时候，第一次用什么切割用过什么 |
### TabPage 操作

| 快捷键                       | 功能                                                                   |
| ---------------------------- | ---------------------------------------------------------------------- |
| `:tabs`                      | 查看所有的 **TabPage**                                                                       |
| `:tablnew`/`:tabe[dit]`      | 创建新的标签页，并创建/打开文件                                        |
| `:tabc[lost]`                | 关闭当前标签页                                                         |
| `:tabo[nly]`                 | 除了当前便签页，关闭其他所有标签页                                     |
| `<N>gt`/`:<count>tabn[ext]`  | 前往第 N 个便签页（注意这里的 N 是页码）<br/> 如果 N 有正负号，下 N 个 |
| `<N>gT`/`:<count>tabp[rev]`  | 前往上 N 个标签页                                                      |
| `g<tab>`                     | 前往上一个跳转的标签页                                                 |
| `:tabfir[st]`/`:tabr[ewind]` | 跳到第一个标签页                                                       |
| `:tabl[ast]`                 | 跳动最后一个标签页                                                     |
| `:tabm[ove] <N>  `           | 将当前变迁页移动到第 N 个标签页                                        |

> 在文件浏览器(`NetRW`)，键入 `t` 在新的标签页打开文件(夹)
> ==标签的页数是从1开始的！==