---
title: "PyTorch 和 Transformers 在 Mac Os X 下安装部署"
date: 2023-04-12T22:34:00+08:00
tags:
- AI
- Apple
---

### 1. 安装的基础环境

**当前测试的机子是**：**Macbook 16 寸，M1 Pro，2021**

**系统环境**:

```
$ uname -a
Darwin Wens-MacBook-Pro.local 22.4.0 Darwin Kernel Version 22.4.0: Mon Mar  6 20:59:28 PST 2023; root:xnu-8796.101.5~3/RELEASE_ARM64_T6000 arm64 arm Darwin
```

**conda 和 python 版本**:

```
$ conda info

     active environment : base
    active env location : /opt/miniconda3
            shell level : 6
       user config file : /Users/nextchen/.condarc
 populated config files : /Users/nextchen/.condarc
          conda version : 23.3.1
    conda-build version : 3.24.0
         python version : 3.11.2.final.0
       virtual packages : __archspec=1=arm64
                          __osx=13.3.1=0
                          __unix=0=0
       base environment : /opt/miniconda3  (writable)
      conda av data dir : /opt/miniconda3/etc/conda
  conda av metadata url : None
           channel URLs : ...
          package cache : /opt/miniconda3/pkgs
                          /Users/nextchen/.conda/pkgs
       envs directories : /opt/miniconda3/envs
                          /Users/nextchen/.conda/envs
               platform : osx-arm64
             user-agent : conda/23.3.1 requests/2.28.1 CPython/3.11.2 Darwin/22.4.0 OSX/13.3.1
                UID:GID : 501:20
             netrc file : /Users/nextchen/.netrc
           offline mode : False
```

### 2. 创建 conda 环境

```
$ conda create -n ai_pytorch python=3
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /opt/miniconda3/envs/ai_pytorch

  added / updated specs:
    - python=3


The following NEW packages will be INSTALLED:

  bzip2              anaconda/pkgs/main/osx-arm64::bzip2-1.0.8-h620ffc9_4
  ca-certificates    anaconda/pkgs/main/osx-arm64::ca-certificates-2023.01.10-hca03da5_0
  certifi            anaconda/pkgs/main/osx-arm64::certifi-2022.12.7-py311hca03da5_0
  libffi             anaconda/pkgs/main/osx-arm64::libffi-3.4.2-hca03da5_6
  ncurses            anaconda/pkgs/main/osx-arm64::ncurses-6.4-h313beb8_0
  openssl            anaconda/pkgs/main/osx-arm64::openssl-1.1.1t-h1a28f6b_0
  pip                anaconda/pkgs/main/osx-arm64::pip-23.0.1-py311hca03da5_0
  python             anaconda/pkgs/main/osx-arm64::python-3.11.2-hc0d8a6c_0
  readline           anaconda/pkgs/main/osx-arm64::readline-8.2-h1a28f6b_0
  setuptools         anaconda/pkgs/main/osx-arm64::setuptools-65.6.3-py311hca03da5_0
  sqlite             anaconda/pkgs/main/osx-arm64::sqlite-3.41.1-h80987f9_0
  tk                 anaconda/pkgs/main/osx-arm64::tk-8.6.12-hb8d0fd4_0
  tzdata             anaconda/pkgs/main/noarch::tzdata-2023c-h04d1e81_0
  wheel              anaconda/pkgs/main/osx-arm64::wheel-0.38.4-py311hca03da5_0
  xz                 anaconda/pkgs/main/osx-arm64::xz-5.2.10-h80987f9_1
  zlib               anaconda/pkgs/main/osx-arm64::zlib-1.2.13-h5a0b063_0


Proceed ([y]/n)? y


Downloading and Extracting Packages

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate ai_pytorch
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

### 3. 安装 pytorch

```
$ conda activate ai_pytorch
$ pip3 install --pre torch --index-url https://download.pytorch.org/whl/nightly/cpu
Looking in indexes: https://download.pytorch.org/whl/nightly/cpu
...
Installing collected packages: mpmath, typing-extensions, sympy, networkx, MarkupSafe, filelock, jinja2, torch
Successfully installed MarkupSafe-2.1.2 filelock-3.9.0 jinja2-3.1.2 mpmath-1.2.1 networkx-3.0rc1 sympy-1.11.1 torch-2.1.0.dev20230411 typing-extensions-4.4.0
```

> ==目前 [Preview (Nightly) build](https://pytorch.org/get-started/locally/) 支持 Apple Silicon GPU 的加速^[1]==

#### 3.1. 测试下你的版本是否支持 Apple Silicon GPU 加速？

```python
import torch
print(f"PyTorch version: {torch.__version__}")

# Check PyTorch has access to MPS (Metal Performance Shader, Apple's GPU architecture)
print(f"Is MPS (Metal Performance Shader) built? {torch.backends.mps.is_built()}")
print(f"Is MPS available? {torch.backends.mps.is_available()}")

# Set the device      
device = "mps" if torch.backends.mps.is_available() else "cpu"
print(f"Using device: {device}")
```

如果运行后和下面的输出一致，说明支持 Apple Silicon GPU 加速^[2]：

```text
PyTorch version: 2.1.0.dev20230411
Is MPS (Metal Performance Shader) built? True
Is MPS available? True
Using device: mps
```

#### 3.2. 验证 Pytorch 的安装是否成功？

```python
import torch
x = torch.rand(5, 3)
print(x)
```

输出应该和下面类似^[3]：

```text
tensor([[0.3380, 0.3845, 0.3217],
        [0.8337, 0.9050, 0.2650],
        [0.2979, 0.7141, 0.9069],
        [0.1449, 0.1132, 0.1375],
        [0.4675, 0.3947, 0.1426]])
```

### 4. 安装 transformers

```
$ pip install transformers
Looking in indexes: http://mirrors.aliyun.com/pypi/simple/
...
Requirement already satisfied: filelock in /opt/miniconda3/envs/ai_pytorch/lib/python3.11/site-packages (from transformers) (3.9.0)
...
Requirement already satisfied: typing-extensions>=3.7.4.3 in /opt/miniconda3/envs/ai_pytorch/lib/python3.11/site-packages (from huggingface-hub<1.0,>=0.11.0->transformers) (4.4.0)
...
Installing collected packages: tokenizers, urllib3, tqdm, regex, pyyaml, packaging, numpy, idna, charset-normalizer, requests, huggingface-hub, transformers
Successfully installed charset-normalizer-3.1.0 huggingface-hub-0.13.4 idna-3.4 numpy-1.24.2 packaging-23.0 pyyaml-6.0 regex-2023.3.23 requests-2.28.2 tokenizers-0.13.3 tqdm-4.65.0 transformers-4.27.4 urllib3-1.26.15
```

#### 验证 Transformers 的安装是否成功？

```python
from transformers import pipeline
classifier = pipeline('sentiment-analysis')
classifier('We are very happy to introduce pipeline to the transformers repository.')
```

输出如下内容^[4]：

```json
[{'label': 'POSITIVE', 'score': 0.9996980428695679}]
```

### 附录：最终的 Python Package 及版本

```
# packages in environment at /opt/miniconda3/envs/ai_pytorch:
#
# Name                    Version                   Build  Channel
bzip2                     1.0.8                h620ffc9_4    defaults
ca-certificates           2023.01.10           hca03da5_0    defaults
certifi                   2022.12.7       py311hca03da5_0    defaults
charset-normalizer        3.1.0                    pypi_0    pypi
filelock                  3.9.0                    pypi_0    pypi
huggingface-hub           0.13.4                   pypi_0    pypi
idna                      3.4                      pypi_0    pypi
jinja2                    3.1.2                    pypi_0    pypi
libffi                    3.4.2                hca03da5_6    defaults
markupsafe                2.1.2                    pypi_0    pypi
mpmath                    1.2.1                    pypi_0    pypi
ncurses                   6.4                  h313beb8_0    defaults
networkx                  3.0rc1                   pypi_0    pypi
numpy                     1.24.2                   pypi_0    pypi
openssl                   1.1.1t               h1a28f6b_0    defaults
packaging                 23.0                     pypi_0    pypi
pip                       23.0.1          py311hca03da5_0    defaults
python                    3.11.2               hc0d8a6c_0    defaults
pyyaml                    6.0                      pypi_0    pypi
readline                  8.2                  h1a28f6b_0    defaults
regex                     2023.3.23                pypi_0    pypi
requests                  2.28.2                   pypi_0    pypi
setuptools                65.6.3          py311hca03da5_0    defaults
sqlite                    3.41.1               h80987f9_0    defaults
sympy                     1.11.1                   pypi_0    pypi
tk                        8.6.12               hb8d0fd4_0    defaults
tokenizers                0.13.3                   pypi_0    pypi
torch                     2.1.0.dev20230411          pypi_0    pypi
tqdm                      4.65.0                   pypi_0    pypi
transformers              4.27.4                   pypi_0    pypi
typing-extensions         4.4.0                    pypi_0    pypi
tzdata                    2023c                h04d1e81_0    defaults
urllib3                   1.26.15                  pypi_0    pypi
wheel                     0.38.4          py311hca03da5_0    defaults
xz                        5.2.10               h80987f9_1    defaults
zlib                      1.2.13               h5a0b063_0    defaults
```

[1]: [Introducing Accelerated PyTorch Training on Mac | PyTorch](https://pytorch.org/blog/introducing-accelerated-pytorch-training-on-mac/)
[2]: [MPS backend — PyTorch master documentation](https://pytorch.org/docs/master/notes/mps.html)
[3]: [Start Locally | PyTorch](https://pytorch.org/get-started/locally/#mac-verification)
[4]: [huggingface/transformers: 🤗 Transformers: State-of-the-art Machine Learning for Pytorch, TensorFlow, and JAX.](https://github.com/huggingface/transformers/#quick-tour)