# Anaconda安装、虚拟环境创建、Gatk4安装
## 1.更新与升级：
sudo命令是用来以管理员权限运行，而apt是Debian/Ubuntu 系统下的包管理器。第一个命令会更新软件包列表，第二个命令会升级所有已安装的软件包
```
sudo apt update
```

## 2.安装Anaconda
**Python** 是生物信息学和人工智能领域的首选语言。WSL 通常会预装一个版本的 Python，但最好安装 Anaconda 或 Miniconda，它们是管理 Python 环境和包的利器，尤其是在处理复杂的生物信息学工具链时。
```
# 1. 先 cd 到根目录下
cd

# 2.下载安装包：在此地址https://www.anaconda.com/download/success 中找到安装包的链接
wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh

# 3.运行安装脚本 anaconda
bash Anaconda3-2024.02-1-Linux-x86_64.sh

# 4. 按照 anaconda 提示进行安装，默认安装到 /home/用户名/anaconda3

# 5. 检查
```

## 3.创建隔离的虚拟环境
```
# 创建虚拟环境(python需查看版本)
conda create -n 虚拟环境名称 python=3.10
#例如：conda create -n my_bioinfo_env python=3.9

# 激活虚拟环境
conda activate my_bioinfo_env
```

## 4.安装生物信息学常用工具，如Gatk4
GATK 是一个由 Broad Institute 开发的、用于分析基因组测序数据并识别变异（例如单核苷酸多态性 SNPs、插入/缺失 InDels 等）的软件工具包。它被认为是基因组变异分析的“黄金标准”。 **核心功能：** 变异检测（Variant Calling）是它的核心功能。它能准确地从测序数据中找出个体基因组与参考基因组之间的差异。
```
#以下方法本质都是添加channel，从各种channel上下载Gatk4；区别在于，一个用的正版官方网，需科学上网；另一则是连接清华镜像站，会快捷很多。由于可以添加多个channel，但是下载时会优先从上往下检索channel，因此要确保快网速的channel在慢网速channel之上（即清华的在top)

# 添加 Bioconda 频道 
conda config --add channels defaults 
conda config --add channels bioconda 
conda config --add channels conda-forg

# 添加清华镜像站，其中conda-forge和bioconda是gatk4所需要的
（base）：~$ site=https://mirrors.tuna.tsinghua.edu.cn/anaconda
（base）：~$ conda config --add channels ${site}/pkgs/free/
（base）：~$ conda config --add channels ${site}/pkgs/main/
（base）：~$ conda config --add channels ${site}/cloud/conda-forge/
（base）：~$ conda config --add channels ${site}/pkgs/r/
（base）：~$ conda config --add channels ${site}/cloud/bioconda/
（base）：~$ conda config --add channels ${site}/cloud/msys2/
（base）：~$ conda config --add channels ${site}/cloud/menpo/
（base）：~$ conda config --add channels ${site}/cloud/pytorch/

# 检查channel
conda config --show channels

# 下载
conda install gatk4
```

### 报错处理
最常见的问题是卡在`Collecting package metadata (repodata.json):`，这通常是网络问题导致的 
解决方法
```
# 移除所有当前的镜像源配置
conda config --remove-key channels

# 按照正确顺序重新添加清华镜像源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

# 可选：如果需要，添加其他常用的镜像源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/

# 清除所有缓存并安装
conda clean --all
conda install gatk4

# 检查安装完成
gatk
```

