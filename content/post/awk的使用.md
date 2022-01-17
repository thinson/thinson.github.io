---
title: "Linux awk是什么"
date: 2022-01-17T17:21:00+08:00
lastmod: 2022-01-17T17:21:00+08:00
draft: false
tags: ["Linux使用"]
categories: ["Linux"]
author: kang

autoCollapseToc: true
# reward: false
# mathjax: false
---
### awk是什么？

`awk`其实不仅仅是工具软件，还是一种编程语言。


用来处理linux系统下的文档

### 基本使用

```bash
awk 条件 动作 文件名
# 可以没有条件
```

```bash
awk -F ':' '{print NR ") " $1}' demo.txt
# 语句用''隔开，''内部字符用引号
# NR表示当前行数
# -F 表示分隔符是什么
 awk -F ':' '{if($1<"m")  print NR ">" $1; else print "____"}' user.txt
```