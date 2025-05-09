---
title: "Golang 项目目录结构规范的思考"
date: 2023-02-20T16:35:00
tags:
- Golang
---

在 **Golang** 中，项目目录结构的规范通常是由==社区共识==形成的。虽然==没有强制==要求按照特定的目录结构组织项目，但是遵循一致的目录结构可以使项目更易于维护、扩展和重构。以下是一个常见的 **Golang** 项目目录规范：

```yaml
- cmd/          # 应用程序的入口和主要执行逻辑
  - main/       # 每个子目录代表一个应用程序
    - main.go
  - app1
    - main.go
- internal/     # 应用程序的内部代码，不能被外部包引用
  - pkg1/
  - pkg2/
  - pkg3/
- pkg/          # 通用的应用程序代码，可以被外部引用
  - pkg1/
- testdata/     # 测试数据，编译的时候会被忽略   
- configs/      # 配置文件
- scripts/      # 项目脚本
- vendor/       # 第三方依赖
- go.mod        # 模块配置文件
- go.sum        # 模块配置文件
```

-   *cmd/* 包含应用程序的入口点和主要执行逻辑，每个子目录代表一个应用程序
-   *internal/* 包含应用程序的内部代码，即不应该被外部包引用的代码
-   *pkg/* 包含应用程序的外部可导出包的代码
-  *configs/* 包含项目的各种配置
-   *vendor/* 包含项目的第三方依赖项
-   *go.mod* 和 *go.sum* 是 **Go modules** 的配置文件

> [golang 编程规范 - 项目目录结构 | MakeOptim](https://makeoptim.com/golang/standards/project-layout/#test)

### 包依赖机制

```
- vendor/
- go.mod
- go.sum
```

#### 依赖的优先级

`go build` 和 `go test` 命令将首先查找 *vendor* 目录中的依赖项版本，如果找不到，则会尝试使用 *go.mod* 文件中列出的依赖项版本。

#### go modules

`go mod` 是 **Golang** 1.11 及以上版本中引入的一种新的依赖管理工具，它可以自动下载、管理和版本控制依赖项，以及确保在构建时使用正确的依赖项版本。

`go.sum` 详细罗列了当前项目直接或间接依赖的所有模块版本，并写明了那些模块版本的 **SHA-256** 哈希值；主要作用是以备 **Golang** 在今后的操作中保证项目所依赖的那些模块版本==不会被篡改==。

> [Go Modules 包管理工具的理解与使用_架构_尚新鹏_InfoQ精选文章](https://www.infoq.cn/article/xyjhjja87y7pvu1iwhz3)
> [5分钟玩转go.sum - 掘金](https://juejin.cn/post/7158069082439286791)

#### vendor

==非必要不用==

`vendor` 目录：**Golang** 编译器会将 `vendor` 目录中的包视为当前项目的本地包，并优先使用该目录中的包进行编译。这在使用第三方包时可以帮助解决依赖管理问题。

#### 建议

==当您使用 go mod 和 vendor 时，最好只使用其中一种方法来管理您的依赖项，以避免潜在的版本冲突和依赖项错误。==

> [go mod 和 go vendor 使用与区别 - 知乎](https://zhuanlan.zhihu.com/p/374044583)

### 硬约束：internal 目录

==编译器层面的约束，不遵守将导致编译失败！==

- 不同项目之间：在 **Golang** 项目中，*internal* 目录通常用于存放不应该被外部包引用的代码，这些代码只能在项目内部使用。 
- 同一个项目内：**Golang** 编译器在导入路径中看到带有 *internal* 的软件包的导入时，它将验证导入包的程序文件是否位于 *internal* 目录的父级目录，或父级目录的子目录中，否则会报错。
	- 例子， *path/to/some/dir/internal/* 可以被 *path/to/some/dir/* 目录下所有代码引用

> [I'll take pkg over internal](https://travisjeffery.com/b/2019/11/i-ll-take-pkg-over-internal/)

### 测试目录和文件

-   *test* 目录：**Golang** 编译器会将 *test* 目录视为测试代码的目录，并会自动运行该目录下的所有测试文件。
-   *_test.go* 文件：**Golang** 编译器会将以 *_test.go* 结尾的文件视为测试文件，并在编译时将其编译为测试代码。

### 其他的社区共识规范

属于软性约束，是一种惯例约定，根据长期开发过程形成的一种共识：
- *cmd/* 包含应用程序的入口点和主要执行逻辑，每个子目录代表一个应用程序；
- *pkg/* 包含应用程序的外部可导出包的代码；
- *internal/* 包含应用程序的内部代码；
- *test/* 包含应用程序的测试代码；

这只是一个通用的示例，实际上可以根据需要自定义项目结构。但是，遵循这些通用最佳实践可以使您的代码更易于理解和维护。

==不要在项目根目录下的 */src* 作为项目主目录，这个是社区共同反对的！==