---
layout: post
title: Docker 批量清理没有标签(tag)的镜像
date: 2023-04-15 03:27:38 +0800
categories: [Blog]
tags: [Docker, 镜像, 清理]
author: sead
summary: Docker 批量清理 none 标签的镜像
---

如果要删除所有  none  标签的镜像，无论是否与任何容器关联，使用以下命令：

```bash
docker rmi $(docker images | grep "none" | awk '/ / { print $3 }')
```

解释一下上述命令的含义： 
 
-  docker images  命令用于列出 Docker 中的镜像。 
-  grep "none"  命令用于筛选出包含  none  标签的镜像。 
-  awk '/ / { print $3 }'  命令用于提取镜像 ID。 
-  $(docker images | grep "none" | awk '/ / { print $3 }')  命令输出的结果将被传递给  docker rmi  命令，以删除这些镜像。 
 
请务必谨慎使用  docker rmi  命令，确保不要删除任何您需要的镜像。