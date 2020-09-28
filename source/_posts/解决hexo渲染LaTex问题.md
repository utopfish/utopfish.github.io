---
title: 解决hexo渲染LaTex问题
date: 2020-09-28 16:42:46
categories:
- bug记录
tags:
- hexo
- bug
- LaTex
---

### 1. 添加插件
>npm install hexo-renderer-mathjax --save

### 2. 在设置中将mathjax设置为可用
theme/_config.yml

```
mathjax:
  enable: true
  per_page: true
```
### 3.在对应文章中加入渲染选项
```
title: 
date: 2020-08-31 20:52:27
categories:
- 
tags:
- 
mathjax: true
---
```