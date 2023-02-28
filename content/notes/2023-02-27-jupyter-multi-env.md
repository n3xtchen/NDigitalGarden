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

首先，在你的 base 环境[^1]中安装 `nb_conda_kernels`

```bash
conda install -c conda-forge jupyterlab
conda install -c conda-forge nb_conda_kernels
```

接着，在需要切换的 **conda** 环境安装 ipykernel

```bash
conda activate {指定的环境}
conda install ipykernel
conda deactivate
```

安装完，刷新下 jupyter lab 页面[^2]就可以识别到新的环境了

[^1]: 就是你启动 jupyter server 的conda env 环境
[^2]: 安装后，不需要重启 jupyter lab