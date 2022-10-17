---
title: "anaconda 安装"
created: ["2022-10-17 20:13"]
tags:
- 博客/
---

下载地址： [https://www.anaconda.com/products/distribution](https://www.anaconda.com/products/distribution)

  

以 linux 为例，下载的是一个 .sh 的文件，通过 bash 运行他即可

`bash Anaconda3-2020.11-Linux-x86_64.sh`

会运行一段交互式脚本，确认安装配置。

### 内网环境配置

`conda config --add channels [https://mirrors.aliyun.com/pypi/simple/](https://mirrors.aliyun.com/pypi/simple/)`

查看添加的镜像：

`conda config --get channels`

推荐使用搜到的 `.condarc`直接复制粘贴

```bash
channels:
- defaults
show_channel_urls: true
channel_alias: http://xxx.com/nexus/repository/anaconda
default_channels:
- http://xxx.com/nexus/repository/anaconda/pkgs/main
- http://xxx.com/nexus/repository/anaconda/pkgs/free
- http://xxx.com/nexus/repository/anaconda/pkgs/r
- http://xxx.com/nexus/repository/anaconda/pkgs/pro
- http://xxx.com/nexus/repository/anaconda/pkgs/msys2
custom_channels:
conda-forge: http://xxx.com/nexus/repository/anaconda
msys2: http://xxx.com/nexus/repository/anaconda
bioconda: http://xxx.com/nexus/repository/anaconda
menpo: http://xxx.com/nexus/repository/anaconda
pytorch: http://xxx.com/nexus/repository/anaconda
simpleitk: http://xxx.com/nexus/repository/anaconda
auto_activate_base: false #用于关闭自动启用 base 环境
```

  

`pip config set global.index-url [https://pypi.tuna.tsinghua.edu.cn/simple](https://pypi.tuna.tsinghua.edu.cn/simple)`

### 环境创建

from： https://www.cnblogs.com/xiaojianliu/p/13466666.html

在Anaconda中conda可以理解为一个工具，也是一个可执行命令，其核心功能是包管理与环境管理。所以对虚拟环境进行创建、删除等操作需要使用conda命令。
```bash
conda 本地环境常用操作
#获取版本号
conda --version 或 conda -V

#检查更新当前conda
conda update conda

#查看当前存在哪些虚拟环境
conda env list 或 conda info -e

#查看--安装--更新--删除包

conda list：
conda search package_name# 查询包
conda install package_name
conda install package_name=1.5.0
conda update package_name
conda remove package_name
conda创建虚拟环境
#创建名为your_env_name的环境
conda create --name your_env_name
#创建制定python版本的环境
conda create --name your_env_name python=2.7
conda create --name your_env_name python=3.6
#创建包含某些包（如numpy，scipy）的环境
conda create --name your_env_name numpy scipy
#创建指定python版本下包含某些包的环境
conda create --name your_env_name python=3.6 numpy scipy
激活虚拟环境
#Linux
source activate your_env_name

#Windows
activate your_env_name
退出虚拟环境
#Linux
source deactivate your_env_name

#Windows
deactivate env_name
删除虚拟环境
conda remove -n your_env_name --all
conda remove --name your_env_name --all
复制某个环境
conda create --name new_env_name --clone old_env_name
在指定环境中管理包
conda list -n your_env_name
conda install --name myenv package_name 
conda remove --name myenv package_name
使用国内 conda 软件源加速
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --set show_channel_urls yes
```

`conda create --name projector python=3.9` 创建一个 3.9 的环境

安装包：

`conda install matplotlib numpy jupyterlab`

启动 jupyter lab

`jupyter lab --ip 0.0.0.0 --port 8888`

### jupyter 代码提示

lab 本来就带自动补全的。按 `tab`键就可以。

lsp

[https://github.com/jupyter-lsp/jupyterlab-lsp](https://github.com/jupyter-lsp/jupyterlab-lsp)

pip install 'jupyterlab>=3.0.0,<4.0.0a0' jupyterlab-lsp
pip install 'python-lsp-server[all]'

注意在侧边栏 extension manager 中启用安装的 extension。也可以手动在侧边栏搜索 lsp 安装而不用 pip 安装然后重启 jupyter。

## 离线安装

有时候需要离线

[Python pip离线安装package方法总结（以TensorFlow为例）](https://imshuai.com/python-pip-install-package-offline-tensorflow)

1.  pip download tensorflow
2.  将目录内容拷贝到目标offline机器（比如/offline_package_dir），并目标offline机器执行pip install --no-index --find-links=file:/offline_package_dir tensorflow