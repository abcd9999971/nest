---
title: Docker 從抗拒到抗拒不了
date: 2025-04-21 10:05:09
tags:
---

docker file -> image -> container

在設定vscode發生的問題

1. 在docker container 設定了name但出來image跟container的名字都不是設定的

```json
{
	"name": "React_Practice_5",
	"build": {
	  "context": "..",
	  "dockerfile": "../Dockerfile"
	},
	"forwardPorts": [3000],
	"postCreateCommand": "npm install",
	
  }
```

最後加上這行
```json
"runArgs": ["--name", "React_Practice_5"] 
```