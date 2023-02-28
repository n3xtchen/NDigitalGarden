---
title: "Jupyter 如何自由切换 conda 虚拟环境"
created: 2023-02-27 17:50
tags:
- python
---

如果你的 **python** 环境有多套，并且你是使用 **conda** 安装的，并且你有强迫症（不希望自己在每一套环境安装 **jupyter**），那么接下来将对你有帮助。

### 上面的需求可以总结如下：

- 有一套主 **conda** 环境，负责运行 jupyter 服务
- 这个 jupyter 服务可以选择本机安装其他 **conda** 环境的 **kernal**
- 其他 conda 环境不需要安装整套的 jupyter 套件 

### 看一下怎么安装？

#### 安装被调用方的内核（ `ipykernel`）

首先，我们在需要切换的 **conda** 环境安装 `ipykernel`，因为 **jupyter** 是通过它来识别和调用环境的

```bash
conda activate {指定的环境}
conda install ipykernel
conda deactivate
```

安装完，刷新下 jupyter lab 页面[^2]就可以识别到新的环境了

#### 让你的 base 环境识别到你已经安装好的内核（`ipykernel`）

##### 方法一：nb_conda_kernels[推荐]

在你的 base 环境[^1]中安装 `nb_conda_kernels`

```bash
conda activate base # base 可以替换成你系统中安装任意一个 env
conda install -c conda-forge nb_conda_kernels
```

重启后，就能识别到系统安装了  `ipykernel` 的 **python** 虚拟环境

##### 方法二：手动关联内核（`ipykernel`）

```bash
conda activate {指定的环境}
ipython kernel install --user --name={指定的环境} # 当然和换成其他名字，但是最好和你的 env 关联
conda deactivate
```

[^1]: 就是你启动 jupyter server 的conda env 环境
[^2]: 安装后，不需要重启 jupyter lab