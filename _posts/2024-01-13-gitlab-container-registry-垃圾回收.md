---
layout: post
title: GitLab Container Registry 垃圾回收
date: 2024-01-13 11:56:46 +0800
categories: [GitLab]
tags: [垃圾回收]
author: sead
summary: todo summary
---

GitLab Container Registry docker 镜像 清理

```bash
sudo gitlab-ctl registry-garbage-collect -m 
```