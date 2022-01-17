---
title: "利用anaconda离线部署python环境"
date: 2022-01-17T17:21:00+08:00
lastmod: 2022-01-17T17:21:00+08:00
draft: false
tags: ["python", "环境"]
categories: ["Python"]
author: kang

autoCollapseToc: true
# reward: false
# mathjax: false
---
> 将开发机的**python开发环境**部署到业务机的**离线生产环境**通常是比较困难的。python版本，软件包的版本和各种依赖环境都要较为严格的保持一致。如果不是十分小心，很容易就会陷入各种链接错误等。如何比较简便的进行python环境的离线迁移呢？本文基于anaconda的包管理功能，提供一种较为简便的环境迁移方法。

### 思路

思路很简单，就是将 开发环境的虚拟环境文件夹 移动到 生产环境的虚拟环境文件夹。

需要保证： python的基础版本一致，如3.6对应3.6，后面小版本无所谓。

## 流程

#### 开发机

1. 开发机使用conda创建好对应的虚拟环境，并安装必要的包。

    ```bash
    # 创建并激活环境
    conda create -n OCEAN python=3.6
    conda activate OCEAN
    # 安装必要的包
    pip install xxx
    conda install xxx
    ```

2. 开发机器的环境打包
   
   首先`conda info -e`找到对应包的位置。如图，我的环境在`/home/wukang/.conda/envs/OCEAN` 目录下。
   
   ![image-20211227175202788](https://gitee.com/kang_wu/pic-bed/raw/master/img/image-20211227175202788.png)
   
   打包对应的环境，`tar cvf OCEAN_ENVS.tar /home/wukang/.conda/envs/OCEAN `，将对应的环境拷贝至U盘。

至此，物理机的操作已经完成，把需要的代码和上述生成的环境压缩包拷贝至U盘即可。

#### 业务机

1. 安装anaconda/miniconda。

2. 创建和开发机器**同名**的虚拟环境，一定要同名，否则后续会出问题。

   ```bash
   # 从base环境克隆出和开发环境同名的OCEAN环境
   conda create -n OCEAN --clone base
   ```

3. 解压环境。`tar xvf OCEAN_ENVS.tar /home/xxx/miniconda/envs/  ` 

4. 激活环境。

   ```bash
   source activate OCEAN
   ```

至此，业务机的环境克隆就已经完成。



