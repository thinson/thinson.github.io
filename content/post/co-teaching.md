---
title: "[论文阅读]Co-teaching"
date: 2022-01-17
tags: ["机器学习"]
categories: ["学术"]
author: kang
---

## 作者

第一完成单位为悉尼科技大学
论文：https://arxiv.org/pdf/1804.06872.pdf
代码： https://github.com/bhanML/Co-teaching

## 背景
co-teaching是nips2018上的文章。主要解决的是noisy label的问题。

## 基础观点
1. DNN在训练的时候，一开始是从clean label中获取信息，训练后期倾向于拟合噪声。
2. 训练前期，loss低的倾向于是clean的label。

## 方法
![最右为co-teaching](https://gitee.com/kang_wu/pic-bed/raw/master/img/11910280-fbb2d3a2c3925326.png)
M-Net 利用DNN本身进行噪声修正，或造成噪声积累。
Co-teaching的思想相当于把当前网络认为clean的样本交给另外的网络训练，避免了噪声累积。网络1把自己认为干净的**一部分**样本交给网络2，网络2把自己认为干净**一部分**的样本交给网络1.
随着训练的进行（epoch变大），互相认为干净的这部分比例逐步减少 ，也就是一开始给网络的数据很多（不管是含有噪声还是没有噪声都扔给网络训练），最后给网络的训练数据逐步减少，**避免了网络对于噪声数据的拟合**，对应基础观点1.

算法具体流程如下：

![算法流程.png](https://upload-images.jianshu.io/upload_images/11910280-22c160a73ba8fb0a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

## 代码
核心代码为梯度互相更新的部分, 相当于先对每个网络各求了loss，再对loss进行排序，选取最低的一定比例交给另一个网络进行反向传播。

loss部分代码如下：
```python
# Loss functions
def loss_coteaching(y_1, y_2, t, forget_rate, ind, noise_or_not):
    loss_1 = F.cross_entropy(y_1, t, reduce = False)
    ind_1_sorted = np.argsort(loss_1.data).cuda()
    loss_1_sorted = loss_1[ind_1_sorted]

    loss_2 = F.cross_entropy(y_2, t, reduce = False)
    ind_2_sorted = np.argsort(loss_2.data).cuda()
    loss_2_sorted = loss_2[ind_2_sorted]

    remember_rate = 1 - forget_rate
    num_remember = int(remember_rate * len(loss_1_sorted))

    pure_ratio_1 = np.sum(noise_or_not[ind[ind_1_sorted[:num_remember]]])/float(num_remember)
    pure_ratio_2 = np.sum(noise_or_not[ind[ind_2_sorted[:num_remember]]])/float(num_remember)

    ind_1_update=ind_1_sorted[:num_remember]
    ind_2_update=ind_2_sorted[:num_remember]
    # exchange
    loss_1_update = F.cross_entropy(y_1[ind_2_update], t[ind_2_update])
    loss_2_update = F.cross_entropy(y_2[ind_1_update], t[ind_1_update])

    return torch.sum(loss_1_update)/num_remember, torch.sum(loss_2_update)/num_remember, pure_ratio_1, pure_ratio_2
```

forget rate更新代码：

```python
# define drop rate schedule
rate_schedule = np.ones(args.n_epoch)*forget_rate
rate_schedule[:args.num_gradual] = np.linspace(0, forget_rate**args.exponent, args.num_gradual)
```

调用：
```python
loss_1, loss_2, pure_ratio_1, pure_ratio_2 = loss_coteaching(logits1, logits2, labels, rate_schedule[epoch], ind, noise_or_not)
```