---
title: "Golang-包（Package）"
date: 2023-03-13T23:54:00
tags:
- Golang
---

- 包必需由目录来承载
- 一个目录下只能有一个包名，否则编译器会报错
- 包名路径中看到带有 *internal* 的软件包的导入时，它将验证导入包的程序文件是否位于 *internal* 目录的父级目录，或父级目录的子目录中，否则会报错。
- 方法和变量要跨包访问，需要首字母大写，否则会报找不到包
	- 所以单元测试最好和所测试的函数在同一个包内，否者私有方法（即首字母飞大写字符的）无法测试

### 导入包

这个是我们演示的代码结构：

```
./test
├── go.mod
├── main.go
├── dir_not_pkg_name
│   └── whatever.go
├── pkg1
│   └── pkg1.go // Echo 方法打印：I am pkg1!
└── pkg2
    └── pkg2.go // Echo 方法打印：I am pkg2!
```

```golang
// main.go
package main

// 一个个导入
// import "test/pkg1"
// import "test/pkg2"
// import pkg "test/pkg1"

// 一行导入多个包
import ("test/pkg1"; "test/pkg1"; pkg "test/pkg1")

// 批量导入
import (
    "test/pkg1"
    "test/pkg2"
    pkg "test/pkg1"
)

func main() {
    pkg1.Echo()
    pkg2.Echo()
    pkg.Echo()
}

// 下面是 go run main.go 的输出结果
// I am pkg1!
// I am pkg2!
// I am pkg1!
```

**注意**：
1. 包名需要使用引号（`"`）包围，很多其他语言都没有，比如 **Python** 和 **Java**;
2. 避免重名或者方便调用，可以使用别名；
3. ==一行导入多个包的 `()` 和 `;` 都不可省略==；

#### 包名和目录名不一致

==声明：不建议包名和包所在的目录名不一致！==

*test/dir_not_pkg_name/whatever.go* 代码如下：

```go
package another_pkg

import "fmt"

func Echo() {
	fmt.Println("Hi!")
}
```

```go
// main.go
package main

import "test/dir_not_pkg_name"

func main() {
	another_pkg.Echo();  // 编译成功后，执行输出 Hi
	
	//dir_not_pkg_name.Echo() 
	// 变异报错：./main.go:11:5: undefined: dir_not_pkg_name
}
```

#### 不支持相对路径，

==golang 不支持相对路径的导入，利大于弊==

```go
// main.go
package main

import "./pkg1"
```

编译的时候，会报如下错误：

```
go build main.go
main.go:3:8: "./pkg1" is relative, but relative import paths are not supported in module mode
```



[A beginners guide to Packages in Golang | CalliCoder](https://www.callicoder.com/golang-packages/)