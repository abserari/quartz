---
title: "CUDA Windows 环境搭建"
created: ["2022-10-17 20:12"]
tags:
- 博客/
---
引用[[anaconda 安装]]

## Windows 环境搭建

### CUDA

[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads?target_os=Windows&target_arch=x86_64&target_version=11&target_type=exe_local)

下载了 windows 下的 CUDA 11.7 的 local 版本，2.5g，下载安装即可

安装完之后重启，可以运行`nvidia-smi.exe`查看一下自己的显卡。

### Conda （python 环境）

下载 miniconda

[https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html) 安装

通过 `code $PROFILE` 启动 vscode 添加该环境到 powershell 自动启动脚本中

```powershell
# 启动 miniconda 环境
D:\miniconda\shell\condabin\conda-hook.ps1 ;conda activate 'D:\miniconda'
```

  
### Pytorch & Jupyter

因为有梯子，速度挺快，就不用镜像源了

[https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/) 找到对应 CUDA 的版本，用的 pip 下载，快一些比 conda。

`pip3 install torch torchvision torchaudio --extra-index-url [https://download.pytorch.org/whl/cu116](https://download.pytorch.org/whl/cu116)`

  

`pip install matplotlib numpy jupyterlab`

## oneflow

[https://docs.oneflow.org/master/index.html](https://docs.oneflow.org/master/index.html)

`python3 -m pip install -f https://release.oneflow.info oneflow==0.8.0+cu112`

### 动手学深度学习

可以从这里下载大量的 nodebook 供学习  

[https://github.com/d2l-ai/d2l-zh](https://github.com/d2l-ai/d2l-zh)