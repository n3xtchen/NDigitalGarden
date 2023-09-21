---
title: "VimScript: 变量声明和基础类型操作"
date: 2023-09-14T13:46:00+08:00
tags:
  - Vim
---
### 变量

#### 声明方式

```vim
let  a = 1      " 声明变量
unlet a         " 删除变量
unlet! a        " 如果 a 不存在，不会报错

const c = 1     " 声明常量
```

#### 作用域

| 标识              | 作用域                           | 变量前缀 | 
| ----------------- | -------------------------------- | -------- |
| buffer-variable   | 在当前缓冲文件内可见             | `b:`     |
| window-variable   | 在当前窗口内可见                 | `w:`     |
| tabpage-variable  | 在当前标签页内可见               | `t:`     |
| global-variable   | 全局可见                         | `g:`     |
| local-variable    | 在当前函数内可见                 | `l:`     |
| script-variable   | 在当前脚本内可见                 | `s:`     |
| function-argument | 函数参数                         | `a:`     |
| vim-variable      | 全局可见（**Vim** 预定义的变量） | `v:`     |

> 详见：`:help variable-scope`
### 基础数据类型

| 类型           | 例子                     | 说明                      |
| -------------- | ------------------------ | ------------------------- |
| 数值型(Number) | `123`,`-123`,`0x10`            |                           |
| 浮点型(Float)  | `123.456`,`3.14e-6`          | 编译时,需要 `+float` 特性 |
| 字符串(String) | `'nextchen'`, `"Hello\"You"` |                           |

### 数值(Number) 的常规操作

| 语法                | 功能               | 用例                                                      |
| ------------------- | ------------------ | --------------------------------------------------------- |
| `a+b`               | 加                 | `1+1 " 结果是 2`                                          |
| `a-b`               | 减                 | `2-2 " 结果是 0`                                          |
| `a*b`               | 乘                 | `2*2 " 结果是 4`                                          |
| `a/b`               | 除                 | `3/2 " 结果是 1，自动类型转换`<br/>`3/2.0 " 结果是 1.5 ` |
| `a%b`               | 求余               | `3/2 " 结果是 1`                                          |
| `abs(x)`            | 绝对值             |                                                           |
| `round(x)`          | 四舍五入           |                                                           |
| `ceil(x)`           | 向上取整           |                                                           |
| `floor(x)`          | 向下取整           |                                                           |
| `exp(x)`            | 自然对数           | $e^x$                                                     |
| `log(x)`/`log10(x)` | 自然（或10）的对数 |                                                           |
| `pow(x, y)`         | N次方              | $x^y$                                                     |
| `sqrt(x)`           | 平方根             |                                                           |
| `isinf(x)`          | 是否无穷大         |                                                           |
| `isnan(x)`          | 是否是个数值       |                                                           |

> 详见文档：`:help float-functions` 和 `:help expr-number`

### 字符串(String) 的常规操作

> 详见文档：`:help string-functions` 和 `:help expr-string`

| 语法                                  | 功能                                                                                                                     |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `len(x)`                              | 计算字符长度                                                                                                             |
| `split(x[, sep='\W\+'])`              | 字符拆封<br/>拆封逻辑默认是空白                                                                                          |
| `join(list[, sep=" "])`               | 字符列表合并                                                                                                             |
| `tolower(x)`/`toupper(x)`             | 转成大小写                                                                                                               |
| `trim(x)`                             | 去除前后空格                                                                                                             |
| `slice(str[, start, count])`          | 字符串截取                                                                                                               |
| `match(str, pat, [start, count])`     | 匹配函数，根据 `pat` 匹配模式在 `str` 中检索；<br/> 如果匹配，返回的是匹配字符串的开始位置； <br/> 如果不匹配，返回 -1； |
| `matchend(str, pat, [start, count])`  | 参数用法和 `match()` 一样; <br/>如果匹配，返回的是匹配字符串的结束位置; <br/>如果不匹配，返回 -1；                       |
| `matchstr(str, pat, [start, count])`  | 参数用法和 `match()` 一样; <br/>如果匹配，返回的是匹配字符串; <br/>如果不匹配，返回 -1；                                 |
| `matchlist(str, pat, [start, count])` | 参数用法和 `match()` 一样; <br/>如果匹配，返回 1 个全匹配和 9 个子匹配; <br/>如果不匹配，返回空列表；                                                                                                                              |
| `matchfuzzy(strlist, str [, dict])`   | 模糊匹配，返回匹配的 `str` 列表;                                                                                                                     |
| `substitute(str, pat, sub, flags)`    | 模式替换，将识别到 `pat` 替换成 `sub`;                                                                                                                         |

> ==`[, a=1, ...]` 代表可选参数 ==

#### 代码 Demo 

```vim
" 计算字符长度
:echo len('n3xtchen')
输出: 8

" 字符串截取
:echo slice('n3xtchen', 4)
输出: chen

" 字符串拆分和合并
" - 不指定 seq 情况，默认是空格
:echo split('n3xtchen is Hero!')
输出: ['n3xtchen', 'is', 'Hero!']
:echo join(['N3xtchen', "Cool!"])
输出: N3xtchen Cool!

" - 指定 seq 的情况
:echo split('/usr/local/share', '/')
输出: ['usr', 'local', 'share'] 
:echo join(['/usr', 'local', 'share'], '/')
输出: /usr/local/share

" 模糊匹配
:echo matchfuzzy(["clay", "crow"], "cay")
输出: ["clay"]
```

**`match()` 系函数**：

```text
匹配模式（pat）： tch
字符: n 3 x [t c h] e n
位置: 0 1 2  3 4 5  6 7
```

> ==0 是初始索引==

```vim
" 匹配 tch 首个字符 t 所在字符串的位置
:echo match('n3xtchen', 'tch')
输出: 3
" 匹配 tch 最后一个字符的下一个字符 e 的位置
:matchend('n3xtchen', 'tch')   
输出: 6

" 如果匹配不到字符的话
:echo match('n3xtchen', '1')
输出: -1
:echo matchend('n3xtchen', '1')
输出: -1

" 如果匹配，返回匹配的字符，如果未匹配，返回空
:echo matchstr('n3xtchen', 'chen')
输出: chen
:echo matchstr('n3xtchen', '1')
输出： <这里是空，没有任何打印>

" 返回一个全匹配（即 matchstr），9 个子匹配
matchlist('n3xtchen', '\(n3xt\)\(chen\)') 
输出: ['n3xtchen', 'n3xt', 'chen', '', '', '', '', '', '', '']
" \0: matchstr('n3xtchen', '\(n3xt\)\(chen\)')
" \1: 匹配第一个括号，即 matchstr('n3xtchen', '\(n3xt\)')
" \2: 匹配第一个括号，即 matchstr('n3xtchen', '\(chen\)')
" \3..\9: 因为只有两个子匹配，即两个括号, 所以第3个子查询之后，都是和空白匹配，都是空白


" 指定查询的开始位置和检索长度
" start: 默认是 0，从第一个字符串开始
" count: 默认是 strlen(str), 即字符的总长度
" 下面的例子等价于 match('n3xtchen', 'tch')
:echo match('n3xtchen', 'tch', 0, 8)
输出: 3
" 从 第三个位置开始检索直到结束
:echo match('n3xtchen', 'tch', 3)
输出: 3
" 从第三个位置开始检索，检索 3 个字符长度
:echo match('n3xtchen', 'tch', 3, 3)
输出: -1
```

**`substidute()` 模式替换**

```vim
" 将 n3xtchen 替换成 Vimer，只替换一次（由第四个参数控制，'' 表示只替换一次）
:echo substitute('n3xtchen is so Cool, because n3xtchen use Vim!', 'n3xtchen', 'Vimer', '')
输出: Vimer is so Cool, because n3xtchen use Vim!
" 'g' 表示全局替换
:echo substitute('n3xtchen is so Cool, because n3xtchen use Vim!', 'n3xtchen', 'Vimer', 'g')
输出: Vimer is so Cool, because Vimer use Vim!
" pat 支持分组正则的
:echo substitute('n3xtchen is Cool!', '\(.*\) is Nice!', '\1', '')
输出: n3xtchen is Nice!
```

###  常见命令

| 代码                    | 说明                                       |
| ----------------------- | ------------------------------------------ |
| `" 这是注释`            | 注释                                       |
| `:echo 'Hi!'`           | 输出到 Vim 终端                            |
| `:echom[sg] 'Hi!'`      | 输出到信息日志，你可以通过 `:message` 查看 |
| `:eval 'echo "Hi!"'`    | 将字符串当作表达式来执行，抛弃结果         |
| `:execute 'echo "Hi!"'` | 将字符串当作表达式来执行，返回结果         | 
