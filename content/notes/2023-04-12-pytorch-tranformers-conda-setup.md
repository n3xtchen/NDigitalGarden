---
title: "PyTorch 和 Transformers: 安装部署 "
date: 2023-04-12T22:34:00+08:00
tags:
- AI
---

### 1. 安装的基础环境

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
Collecting torch
  Downloading https://download.pytorch.org/whl/nightly/cpu/torch-2.1.0.dev20230411-cp311-none-macosx_11_0_arm64.whl (56.6 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 56.6/56.6 MB 108.8 kB/s eta 0:00:00
Collecting filelock
  Downloading https://download.pytorch.org/whl/nightly/filelock-3.9.0-py3-none-any.whl (9.7 kB)
Collecting typing-extensions
  Downloading https://download.pytorch.org/whl/nightly/typing_extensions-4.4.0-py3-none-any.whl (26 kB)
Collecting sympy
  Downloading https://download.pytorch.org/whl/nightly/sympy-1.11.1-py3-none-any.whl (6.5 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 6.5/6.5 MB 59.4 kB/s eta 0:00:00
Collecting networkx
  Downloading https://download.pytorch.org/whl/nightly/networkx-3.0rc1-py3-none-any.whl (2.0 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.0/2.0 MB 38.5 kB/s eta 0:00:00
Collecting jinja2
  Downloading https://download.pytorch.org/whl/nightly/Jinja2-3.1.2-py3-none-any.whl (133 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.1/133.1 kB 65.6 kB/s eta 0:00:00
Collecting MarkupSafe>=2.0
  Downloading https://download.pytorch.org/whl/nightly/MarkupSafe-2.1.2-cp311-cp311-macosx_10_9_universal2.whl (17 kB)
Collecting mpmath>=0.19
  Downloading https://download.pytorch.org/whl/nightly/mpmath-1.2.1-py3-none-any.whl (532 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 532.6/532.6 kB 55.0 kB/s eta 0:00:00
Installing collected packages: mpmath, typing-extensions, sympy, networkx, MarkupSafe, filelock, jinja2, torch
Successfully installed MarkupSafe-2.1.2 filelock-3.9.0 jinja2-3.1.2 mpmath-1.2.1 networkx-3.0rc1 sympy-1.11.1 torch-2.1.0.dev20230411 typing-extensions-4.4.0
```

```
# packages in environment at /opt/miniconda3/envs/ai_pytorch:
#
# Name                    Version                   Build  Channel
bzip2                     1.0.8                h620ffc9_4    defaults
ca-certificates           2023.01.10           hca03da5_0    defaults
certifi                   2022.12.7       py311hca03da5_0    defaults
filelock                  3.9.0                    pypi_0    pypi
jinja2                    3.1.2                    pypi_0    pypi
libffi                    3.4.2                hca03da5_6    defaults
markupsafe                2.1.2                    pypi_0    pypi
mpmath                    1.2.1                    pypi_0    pypi
ncurses                   6.4                  h313beb8_0    defaults
networkx                  3.0rc1                   pypi_0    pypi
openssl                   1.1.1t               h1a28f6b_0    defaults
pip                       23.0.1          py311hca03da5_0    defaults
python                    3.11.2               hc0d8a6c_0    defaults
readline                  8.2                  h1a28f6b_0    defaults
setuptools                65.6.3          py311hca03da5_0    defaults
sqlite                    3.41.1               h80987f9_0    defaults
sympy                     1.11.1                   pypi_0    pypi
tk                        8.6.12               hb8d0fd4_0    defaults
torch                     2.1.0.dev20230411          pypi_0    pypi
typing-extensions         4.4.0                    pypi_0    pypi
tzdata                    2023c                h04d1e81_0    defaults
wheel                     0.38.4          py311hca03da5_0    defaults
xz                        5.2.10               h80987f9_1    defaults
```

### 4. 安装 transformers

```
pip install transformers
Looking in indexes: http://mirrors.aliyun.com/pypi/simple/
Collecting transformers
  Downloading http://mirrors.aliyun.com/pypi/packages/87/f0/2a152ed10ab8601431e87a606d397f7473c5fa4f8162f4ec5bda6ddb2df4/transformers-4.27.4-py3-none-any.whl (6.8 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 6.8/6.8 MB 590.5 kB/s eta 0:00:00
Requirement already satisfied: filelock in /opt/miniconda3/envs/ai_pytorch/lib/python3.11/site-packages (from transformers) (3.9.0)
Collecting huggingface-hub<1.0,>=0.11.0
  Downloading http://mirrors.aliyun.com/pypi/packages/df/90/5ad98abead047169f4f86bc67e99020c841d71c9c6bd202e04af71e70e53/huggingface_hub-0.13.4-py3-none-any.whl (200 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 200.1/200.1 kB 417.4 kB/s eta 0:00:00
Collecting numpy>=1.17
  Downloading http://mirrors.aliyun.com/pypi/packages/01/04/a8b0bb5ffd6b36cb9ff9b67ca6966d55c4a9fdb40ace81a2b33d1559c3b7/numpy-1.24.2-cp311-cp311-macosx_11_0_arm64.whl (13.8 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 13.8/13.8 MB 659.7 kB/s eta 0:00:00
Collecting packaging>=20.0
  Downloading http://mirrors.aliyun.com/pypi/packages/ed/35/a31aed2993e398f6b09a790a181a7927eb14610ee8bbf02dc14d31677f1c/packaging-23.0-py3-none-any.whl (42 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 42.7/42.7 kB 273.5 kB/s eta 0:00:00
Collecting pyyaml>=5.1
  Downloading http://mirrors.aliyun.com/pypi/packages/cb/5f/05dd91f5046e2256e35d885f3b8f0f280148568f08e1bf20421887523e9a/PyYAML-6.0-cp311-cp311-macosx_11_0_arm64.whl (167 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 167.5/167.5 kB 828.8 kB/s eta 0:00:00
Collecting regex!=2019.12.17
  Downloading http://mirrors.aliyun.com/pypi/packages/2b/c2/12f0de728011be620421cec5884f41b651176821487b67631cc093585215/regex-2023.3.23-cp311-cp311-macosx_11_0_arm64.whl (288 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 288.8/288.8 kB 638.0 kB/s eta 0:00:00
Collecting requests
  Downloading http://mirrors.aliyun.com/pypi/packages/d2/f4/274d1dbe96b41cf4e0efb70cbced278ffd61b5c7bb70338b62af94ccb25b/requests-2.28.2-py3-none-any.whl (62 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 62.8/62.8 kB 333.0 kB/s eta 0:00:00
Collecting tokenizers!=0.11.3,<0.14,>=0.11.1
  Downloading http://mirrors.aliyun.com/pypi/packages/0c/e0/f51b2d52fcc2c64e0b81da0a1c68d57b3859212143dbc64b0d175ed78693/tokenizers-0.13.3-cp311-cp311-macosx_12_0_arm64.whl (3.9 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 3.9/3.9 MB 696.3 kB/s eta 0:00:00
Collecting tqdm>=4.27
  Downloading http://mirrors.aliyun.com/pypi/packages/e6/02/a2cff6306177ae6bc73bc0665065de51dfb3b9db7373e122e2735faf0d97/tqdm-4.65.0-py3-none-any.whl (77 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 77.1/77.1 kB 402.3 kB/s eta 0:00:00
Requirement already satisfied: typing-extensions>=3.7.4.3 in /opt/miniconda3/envs/ai_pytorch/lib/python3.11/site-packages (from huggingface-hub<1.0,>=0.11.0->transformers) (4.4.0)
Collecting charset-normalizer<4,>=2
  Downloading http://mirrors.aliyun.com/pypi/packages/85/e8/18d408d8fe29a56012c10d6b15960940b83f06620e9d7481581cdc6d9901/charset_normalizer-3.1.0-cp311-cp311-macosx_11_0_arm64.whl (121 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 121.7/121.7 kB 102.5 kB/s eta 0:00:00
Collecting idna<4,>=2.5
  Downloading http://mirrors.aliyun.com/pypi/packages/fc/34/3030de6f1370931b9dbb4dad48f6ab1015ab1d32447850b9fc94e60097be/idna-3.4-py3-none-any.whl (61 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 61.5/61.5 kB 343.8 kB/s eta 0:00:00
Collecting urllib3<1.27,>=1.21.1
  Downloading http://mirrors.aliyun.com/pypi/packages/7b/f5/890a0baca17a61c1f92f72b81d3c31523c99bec609e60c292ea55b387ae8/urllib3-1.26.15-py2.py3-none-any.whl (140 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 140.9/140.9 kB 374.3 kB/s eta 0:00:00
Requirement already satisfied: certifi>=2017.4.17 in /opt/miniconda3/envs/ai_pytorch/lib/python3.11/site-packages (from requests->transformers) (2022.12.7)
Installing collected packages: tokenizers, urllib3, tqdm, regex, pyyaml, packaging, numpy, idna, charset-normalizer, requests, huggingface-hub, transformers
Successfully installed charset-normalizer-3.1.0 huggingface-hub-0.13.4 idna-3.4 numpy-1.24.2 packaging-23.0 pyyaml-6.0 regex-2023.3.23 requests-2.28.2 tokenizers-0.13.3 tqdm-4.65.0 transformers-4.27.4 urllib3-1.26.15
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