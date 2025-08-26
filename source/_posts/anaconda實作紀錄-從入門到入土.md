---
title: anaconda實作紀錄(從入門到入土)
date: 2025-08-21 14:15:51
tags:
---

要做輪讀的project所以稍微紀錄一下使用的流程，才不會看起來不學無術(你是)


> It's recommended to create a new Python environment via Anaconda for this installation and install PyTorch 2.5.1 (or higher) via pip following https://pytorch.org/. If you have a PyTorch version lower than 2.5.1 in your current environment, the installation command above will try to upgrade it to the latest PyTorch version using pip.

謝謝官方提示增加我的工作量

```
conda create -n <env-name>
> zsh: command not found: conda
```

???我明明以前實驗用過啊...看起來刪掉了，所以喜+100分鐘待機時間

