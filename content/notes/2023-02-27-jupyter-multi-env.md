---
title: "Jupyter 如何自由切换 conda 虚拟环境"
date: 2023-02-27T17:50:00
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

安装完，刷新下 jupyter lab 页面[^2]就可以识别到新的环境了。

#### 让你的 base 环境识别到你已经安装好的内核（`ipykernel`）
##### 方法一：nb_conda_kernels[推荐]

在你的 **base** 环境[^1]中安装 `nb_conda_kernels`

```bash
conda activate base # base 可以替换成你系统中安装任意一个 env
conda install -c conda-forge nb_conda_kernels
```

重启后，就能识别到系统安装了  `ipykernel` 的 **python** 虚拟环境

**简单讲解下原理**[^4]：
1. `get_all_envs`: 通过 conda info 找出所有的可用 env 环境；
2. `get_all_sepecs`: 扫描每一个 env 环境根目录下的 *share/jupyter/kernels/*  中的内核文件；
3. 选择并载入制定的环境的内核。

##### 方法二：手动关联内核（`ipykernel`）

将内核配置安装给 **base** 环境的 **jupyter** 可以识别的 目录[^3]

```bash
conda activate test  # test 可以指定的你任何希望被jupyter识别的env
ipython kernel install --user --name=test # 当然和换成其他名字，但是最好和你的 env 关联
conda deactivate
```

可以通过下面名查看你的生成文件：

```bash
cat jupyter --data-dir`/kernels/test/kernel.json # test替换成你的环境
```

**简单讲解下原理**：
1. client 端把内核配置信息生成到指定目录；
2. base 端扫描指定目录，载入内核。

[^1]: 就是你启动 jupyter server 的conda env 环境
[^2]: 安装后，不需要重启 jupyter lab
[^3]: 可以通过 `jupyter --data-dir` 查看你的内核配置文件
[^4]: [Anaconda-Platform/nb_conda_kernels: Package for managing conda environment-based kernels inside of Jupyter](https://github.com/Anaconda-Platform/nb_conda_kernels)