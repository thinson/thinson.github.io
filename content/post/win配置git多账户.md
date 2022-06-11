---
title: "Win配置git多账户"
date: 2022-06-11T11:18:36+08:00
tags: ["环境", "开发"]
draft: false
---

### 前言

在开发过程中经常会使用多个git账号，如公司的git地址和github的地址不一致，这样很容易出现认证错误的问题。如permission denied等。



### 过程

使用`key-gen`指令生成两个key。

将两个key的`.pub`文件分别上传到github or gitee个人SSH key上。

本地修改`~/.ssh/config`文件

如下图：

```
Host github_thinson
    HostName github.com
    User thinson
    PreferredAuthentications publickey
    IdentityFile C:\\Users\\LAB1314\\.ssh\\github_thinson

Host gitee_xxx
    HostName gitee.com
    User xxx
    PreferredAuthentications publickey
    IdentityFile C:\\Users\\LAB1314\\.ssh\\id_rsa
```

测试过程是

`ssh -T github_thinson`

`ssh -T gitee_xxx`

### 一些坑

1. 添加了key还是permission denied

   执行 `ssh-add  ~/.ssh/id_ras`

2. 执行 `ssh-add  ~/.ssh/id_ras`报错 Could not open a connection to your authentication agent

   `ssh-agent bash`

   `ssh-add ~/.ssh/id_ras`
