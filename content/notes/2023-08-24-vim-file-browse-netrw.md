---
title: "Vim: 原生的文件浏览器功能（NetRW）"
date: 2023-08-24T14:14:00+08:00
tags:
- Vim
---

==不是所有的 Vim 都安装 NERDTree，但是 NetRW 是，所以你应该熟练掌握它！==

多年来（包括现在），**NERDTree** 被认为 **Vim** 事实上的文件浏览器，功能完善，用户体验好，已经深入人心。但是，**Vimer** 为什么抛弃那么多功能齐全，体验完美的 **IDE** 呢？因为我们追求轻量、简洁和快速。而 **netrw** 满足我们的追求，作为 **Vim** 标准插件，你无需安装，简单配置即可使用。 

**Vim 7.1a**（2007年）官方已经将 **NetRW** 作为默认的文件管理器，在很长的时间里，各种不稳定以及 **NERDTree** 的完美体验，导致它使用率都不高。从今天看，稳定多了，我觉得大家可以尝试下。

> **为什么 NetRW 替代 VimExplorer（VE）[^1]？**
> 从 **Vim 7.0** 起，NetRW 取代 VE，成为 **Vim** 默认的文件浏览器，原因是两者有大量重复代码，为了远程和本地浏览体验一致，所以合并到 **NetRW** 中（文档祥见 `help new-netrw-explor`）。 

### 启用 NetRw

```vim
set nocp                    " 'compatible' is not set
filetype plugin on          " plugins are enabled
```

> 确保你的 **Vim** 版本高于 **7.1a**（2023年的今天，这句话是个废话）

### 打开文件浏览器

| 快捷键            | 功能               | 说明 |
| ----------------- | ------------------ | ---- |
| `:Lexplore`       | 编辑器左侧切换     |      |
| `:Sexplore [dir]` | 在当前窗口上方打开 |      |
| `:Vexplore [dir]` | 在当前窗口左侧打开 |      |
| `:Texplore`       | 在新标签页打开     |      |

#### 文件浏览器的展示方式

```
" ============================================================================
" Netrw Directory Listing                                        (netrw v172)
"   {当前目录路径}
"   Sorted by      {name|time|size|extern} {reversed}
"   Sort sequence:  {但使用 name 来排序的时候，可以自定义顺序；其他排序不会出现这个}
"   Copy/Move Tgt: {拷贝/移动 目标目录} (local)
"   Hiding|Showing: {隐藏的正则表达式}
"   Quick Help: <F1>:help  -:go up dir  D:delete  R:rename  s:sort-by  x:special
" ==============================================================================
../
./
autoload/
colors/
compiler/
doc/
ftplugin/
...
```

#### 位置解读

| 行数 | 位置              | 显示情况              | 配置的变量          |
| ---- | ----------------- | --------------------- | ------------------- |
| 1    | 横幅开始          | 必现                  |                     |
| 2    | **NetRW** 版本    | 必现                  |                     |
| 3    | 当前目录          | 必现                  |                     |
| 4    | 排序方式          | 必现                  |                     |
| 5    | Name 排序定义     | `name` 排序模式下展示 |                     |
| 6    | 拷贝/移动目标目录 | `mt` 标记的时候       |                     |
| 7    | 隐藏模式          | 非必现                | `g:netrw_list_hide` |
| 8    | 帮助              | 必现                  |                     |
| 9    | 横幅结束          | 必现                  |                     |
| 剩下 | 文件列表          |                       |                     |

#### 快捷键

| 快捷键                     | 功能                 | 影响的位置       | 说明                                 |
| -------------------------- | -------------------- | ---------------- | ------------------------------------ |
| `I`                        | 横幅展示开关         | `" ...`          | `"` 注释的部分都是横幅               |
| `s`                        | 排序切换             | `Sorted by`      |                                      | 
| `r`                        | 反序开关             | `Sorted by`      | 控制 `reversed` 是否有效             |
| `a`                        | 关闭/展示/隐藏切换   | `Hide\|Showing:` | 空白、Hiding 和 Showing </br> 会交替 |
| `gh`                       | 展示/隐藏开关        | `Hide\|Showing:` |                                      |
| `i`                        | 切换文件列表形式     | 文件列表         | `g:netrw_liststyle`                  |
| 在 `Quick Help` 上 `Enter` | 查看其他帮助命令     | `Quick Help:`    |                                      |
| `mt`                       | 目标目录开关         | `Copy/Move Tgt:` | 仅用于拷贝和移动操作                 |
| `mf`                       | 标记光标下的文件     | 文件列表         | 用于拷贝和移动                       |
| `mF`                       | 取消光标下的标记文件 | 文件列表         |                                      |
| `mu`                       | 取消所有标记文件     | 文件列表         |                                      |

#### 切换文件列表方式（`i`）

**使用建议**：
- ==如果要对文件进行变更操作，建议是用 Tree 之外的其他模式==
- ==纯粹用于查看，如果对文件进行变更操作，会有 Bug[^2]==
- **Tree 模式**长的有点像 **NERTree**

##### Thin 模式 

```vim
let g:netrw_liststyle=0
```

```
../
./
autoload/
colors/
compiler/
doc/
ftplugin/
...
```

##### Long 模式

```vim
let g:netrw_liststyle=1
```

```
../                               0 二  7/11 16:56:22 2023
./                                0 二  7/11 16:56:22 2023
autoload/                         0 二  7/11 16:56:22 2023
bugreport.vim                     1927 二  7/11 16:56:22 2023
colors/                           0 二  7/11 16:56:22 2023
compiler/                         0 二  7/11 16:56:22 2023
...
```

##### Wide 模式

```vim
let g:netrw_liststyle=2
```

```
../                  compiler/            indent/              pack/        ...  
```

##### Tree 模式

```vim
let g:netrw_liststyle=3
```

```
../
vim90/
| autoload/
| | dist/
| | xml/
| | zig/
| | README.txt
...
```

#### 隐藏和显示文件(夹)

隐藏正则的配置：

```vim
let g:netrw_list_hide=\(^\|\s\s\)\zs\.\S\+ " 这个是默认的
```

按下 `a` 会有 3 种情况轮替：
1. 显示所有；
2. 隐藏匹配隐藏正则的文件；
3. 仅显示匹配隐藏正则的文件

按下 `gh` 会有 2 种情况轮替：
1. 显示所有；
2. 隐藏匹配隐藏正则的文件；

> `netrw-gitignore`： **NetRW** 提供 **Git** 忽略文件，可以通过如下配置使用
>    `let g:netrw_list_hide="{你自己定义的隐藏正则},".netrw_gitignore#Hide()`

#### 标记文件(夹)

| 快捷键                                    | 功能                   | 说明     |
| ----------------------------------------- | ---------------------- | -------- |
| `mf`                                      | 标记光标下的文件       | 会有高亮 |
| `mr`                                      | 根据正则表达式标记文件 |          |
| `mF`                                      | 取消光标下的被标记文件 |          |
| `mu`                                      | 取消所有的编辑文件     |          |
| `mh`                                      | 隐藏/显示被标记文件    |          |
| `:echo netrw#Expose("netrwmarkfilelist")` | 查看被标记的文件       |          |

#### 标记传入 vim 的文件列表

```
vim file1 file2
:args
Output: file1 file2
mA
:echo netrw#Expose("netrwmarkfilelist")
Output: ['path/to/file1', 'path/to/file2']
```

#### 将标记的文件追加到传入 vim 的文件列表之后

```
vim
:args
Output: 空白
（marks file1）
ma
:args
[file1]
```

```
vim file1
:args
Output: [file1]
（marks file2）
ma
:args
[file1] file2
```

==netrw 高级的操作都需要这个，所以要注意学习！==
### 打开文件和窗口/标签操作

#### 打开文件
作用在光标上的文件，0 都是默认的；

| 快捷键 | 功能     | 说明                                                                                                                                                                                                                                                                                                                                                               |
| ------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `<CR>`   | 直接打开 | 控制参数 `g:netrw_browse_split`，配置如下: <br/> 0：在当前打开，即覆盖文件浏览器的**窗口** </br>1：每次在文件浏览器的下方打开，越靠近浏览器，打开的时间越晚 <br/>2：每次在文件浏览器的右侧打开，越靠近浏览器，打开的时间越晚<br />3：在新的标签页打开<br/>4（==NERDTree 风格==）：第一次打开的时候，在浏览器右侧，后面没错打开，<br/>都会覆盖第一次打开的**窗口** |
| `o`    | 水平打开 | 控制的参数 `g:netrw_alto`，配置如下<br/>0：在文件浏览器上方打开<br/>1（==NERDTree 风格==）：在文件浏览器下方打开                                                                                                                                                                                                                                                   |
| `v`    | 垂直打开 | 控制的参数 `g:netrw_altv`，配置如下<br/>0：在文件浏览器左侧打开<br/>1（==NERDTree 风格==）：在文件浏览器右侧打开                                                                                                                                                                                                                                                   |
| `p`    | 预览文件 | 在编辑器的上方打开文件，上面几种方式都会把光标移动打开的文件，<br/>该模式下回仍然停留在文件浏览器                                                                                                                                                                                                                                                                       | 

##### 指定最后打开的窗口(Window)

`g:netrw_browse_split` 需要设置成大于 `0` 的时候，最后打开的**窗口**就是 **NetRW** 最后打开的**窗口**，当你按回车的时候，新打开的文件永远会覆盖这个**窗口**。

在实际使用的时候，你打开多个**窗口**，你在浏览文件的时候，你可能想要覆盖掉某一个 **Window**，假设你打开的 3 个**窗口**（并排），你想要覆盖第三个**窗口**，你会怎么做呢？

1. ==`g:netrw_browse_split` 需要设置成 `0`==（这一步折腾了我两天，大家要注意），`g:netrw_chgwin` 才能生效，否则他永远都是定值；
2. 导航到你想要覆盖的**窗口**，通过 `winnr()` 查看当前的**窗口**编号；
3. 通过下面两个方法设置 `g:netrw_chgwin`（他们是等价的）：
	1. `:let g:netrw_chgwin={填入第二步获取的窗口号}`
	2. 在 **NetRW** **窗口**中，键入 `<填入第二步获取的窗口号>C`

你会看到 `editing window now set to window#<填入第二步获取的窗口编号>`，说明你配置成功，你回车的时候就可以获得你的预期了。
#### 目录跳转记录和文件打开记录

| 快捷键 | 功能                                   |
| ------ | -------------------------------------- |
| `P`    | 在上一个打开**窗口**上编辑文件                |
| `qb`   | 查看历史目录跳转记录                   |
| `u`    | 跳转上一个目录                         |
| `U`    | 跳转下一个目录                         |
| `-`    | 跳到父目录                             |
| `gn`   | 将当前光标所在的文件夹设置为当前根目录(树型列表模式下，可用) | 
#### 窗口跳转

| 快捷键                   | 功能                     |
| ------------------------ | ------------------------ |
| `Ctrl-W` `h`/`j`/`k`/`l` | 上/下/左/右跳转          |
| `Ctrl-W` `=`             | 窗口等分，除了固定宽度外 |
| `Ctrl-W` `+`/`-`         | 窗口上/下移动            |
| `Ctrl-W` `<`/`>`         | 窗口左/右移动            |
| `Ctrl-W` `H`/`I`/`J`/`K` | 移动窗口到上/下/左/右    | 
| `Ctrl-W` `t`/`b`         | 如果是水平分割，最左边，最右边，如果垂直分割，最上面最下面<br/> 混合切割的时候，第一次用什么切割用过什么                        |
### 标签页(Tab)操作

在文件浏览器，键入 `t` 在新的标签页打开文件(夹)

==下面是标签操作，和 NetRW 无关 ==

| 快捷键                       | 功能                                                              |
| ---------------------------- | ----------------------------------------------------------------- |
| `:tablnew`/`:tabe[dit]`      | 创建新的标签页，并创建/打开文件                                   |
| `:tabc[lost]`                | 关闭当前标签页                                                    |
| `:tabo[nly]`                 | 除了当前便签页，关闭其他所有标签页                                |
| `<N>gt`/`:<count>tabn[ext]`  | 前往第 N 个便签页（注意这里的 N 是页码）<br/> 如果 N 有正负号，下 N 个 | 
| `<N>gT`/`:<count>tabp[rev]`  | 前往上 N 个标签页                                                 |
| `g<tab>`                     | 前往上一个跳转的标签页                                            |
| `:tabfir[st]`/`:tabr[ewind]` | 跳到第一个标签页                                                  |
| `:tabl[ast]`                 | 跳动最后一个标签页                                                |
| `:tabm[ove] <N>  `           | 将当前变迁页移动到第 N 个标签页                                   |

==标签的页数是从1开始的！==
### 操作和执行文件(夹)

| 快捷键   | 功能                             | 执行命令(=默认命令)                                                                                                                                                       |
| -------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `d`      | 创建目录                         | `let g:netrw_mkdir_cmd=ssh HOSTNAME mkdir`                                                                                                                                |
| `%`      | 创建文件                         |                                                                                                                                                                           |
| `D`      | 删除光标下的文件(夹)             | `let g:netrw_rm_cmd=ssh HOSTNAME rm(dir)`                                                                                                                                 |
| `R`      | 重命名光标下的文件               | `let g:netrw_rename_cmd=ssh HOSTNAME mv`                                                                                                                                  |
| `qf`     | 显示当前光标文件下的文件信息     |                                                                                                                                                                           |
| `gp`     | 改变当前光标下文件的权限         |                                                                                                                                                                           |
| `x`/`gx` | 使用系统文件执行光标下的文件     | 如 html，会用浏览器打开                                                                                                                                                   |
| `X`      | 使用 `System()` 执行光标下的文件 | 官方有 bug，使用 `expand("cword")`，导致无法获取<br/>正确的路径，可以使用下面的配置来获取预期的功能：<br/> `noremap <buffer> X  :<C-U>echo system(expand("cfile:p"))<CR>` |
| `gd`     | 强制作为文件夹对待[^3]               | 仅针对远程文件场景                                                                                                                                                        |
| `gf`     | 强制作为文件对待                 | 仅针对远程文件场景                                                                                                                                                        | 

> `D` : 如果没有标记文件，删除光标下的文件；如果有标记，删除标记的文件；
> `R`：如果没有标记文件，重命名光标下的文件；如果有标记，逐一给标记的文件重命名；


####  Bug：由 `X`  触发的 `**error** (netrw) the <XX> is not executable!`

#### 现象
```
(光标 file1.sh 的 file1 上)
X
Output：**error** (netrw) the file1 is not executable! 
```
##### 排查过程

```
:map X
Output: map X :<C-U>call <SNR>24_NetrwLocalExecute(expand("<cword>"))
```

问题就在 `expand("<cword>")`，这个获取光标下一个词，显然不是我们想要结果，我们期待的结果到是光标下的文件路径，所以需要使用 `expand("<cfile>|p")`

##### 改造

```
(光标 file1.sh 的 file1 上)
:noremap <buffer> X  :<C-U>echomsg system(expand("<cfile>:p"))<CR>
Output: 输出该执行文件的结果
```

> 可以在 `.vimrc` 结尾添加 `noremap <buffer> X  :<C-U>echomsg system(expand("<cfile>:p"))<CR>` 来修复这个 Bug。
 
#### 利用标记文件(夹)的操作

| 快捷键 | 功能                 | 说明                                           |
| ------ | -------------------- | ---------------------------------------------- |
| `md`   | `vimdiff` 标记的文件 |                                                |
| `me`   | 逐一编辑标记的文件   | use :bn, :bp to navigate files; :Rex to return |
| `mg`   | `vimgrep` 标记的文件 | (use :cn, :cp to navigate, :Rex to return)     |
| `ms`   | `source` 标记的文件  | `:so`                                          |
| `mz`   | 逐一压缩标记的文件   | 不会保留原来的文件                             |
| `O`    | 从远端获取文件       |                                                |

#### 拷贝和移动文件(夹)

==不要在 tree 模式下，会出现不可理解的情况，比如文件夹会显示成文件，mt 操作会跳出新的窗口。==

1. 导航你想要拷贝/移动*目标文件夹*，按下  `mt`，你会看到当前目录会出现在横幅的 `Copy/Move Tgt`;
2. 导航到你想要拷贝/移动的文件(夹)，光标指向这个文件，按下 `mf` 标记文件，你会看到标记的文件上会出现高亮；
	- 如果有多个文件，可以多次编辑；
3. 按下 `mc`(复制)/`mm`(移动)

> `mt` 要在 mf 之前，文件夹跳转会导致标记文件 **buff** 重置，会出现错误：**error** (netrw) there are no marked files in this window (:help netrw-mf)

#### 执行文件

`mx` 将键入命令逐一应用在每一个标记的文件中，而 `mX` 将标记的文件列表附在键入的命令之后；很抽象，看几个例子就知道了，下面假设标记了 *file1* 和 *file2* 两个文件：

```
(mark files)  
mx  
Enter command: cat  

底层执行的命令:  
	cat 'file1'  
	cat 'file2'
```

```
(mark files)  
mX  
Enter command: cat  

底层执行的命令:  
	cat 'file1' 'file2'
```

`mv` 执行的 **Ex** 命令：

```
(mark files)  
mv  
Enter vim command: %s/\s\+$//      " 去除行末空格

底层执行的命令(给每一个文件去除行末空格):  
vim -c ":%s/\s\+$//" -c ":update" -c ":quit" (mark files)
```

### 文件标签操作

| 快捷键 | 说明                                                                                         | 文档                                                            |
| ------ | -------------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| gb     | Go to previous bookmarked directory                                                          | [netrw-gb](https://vim-jp.org/vimdoc-en/pi_netrw.html#netrw-gb) |
| mb     | Bookmark current directory                                                                   | [netrw-mb](https://vim-jp.org/vimdoc-en/pi_netrw.html#netrw-mb) |
| qF     | Mark files using a quickfix list                                                             | [netrw-qF](https://vim-jp.org/vimdoc-en/pi_netrw.html#netrw-qF) |
| qL     | Mark files using a [location-list](https://vim-jp.org/vimdoc-en/quickfix.html#location-list) | [netrw-q](https://vim-jp.org/vimdoc-en/pi_netrw.html#netrw-qL)  | 

[^1]: [VimExplorer - VE - the File Manager within Vim! : vim online](https://www.vim.org/scripts/script.php?script_id=1950)
[^2]: [VIM netrw shows directories as files (tree view) - Stack Overflow](https://stackoverflow.com/questions/61074861/vim-netrw-shows-directories-as-files-tree-view)
[^3]: NetRw: 远程符号链接（例如通过 SSH 或 FTP 列出的符号链接）存在问题，因为很难确定它们是链接到文件还是链接到目录。