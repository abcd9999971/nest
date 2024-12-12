---
title: hexo日誌
date: 2024-12-12 13:39:58
tags:
---
## 問題1
在本地運行
'''
Hexo server 
'''
可行

但上傳至github再=生成page時遭遇build error

'''
Logging at level: debug Configuration file: /github/workspace/./_config.yml Theme: landscape github-pages 232 | Error: The landscape theme could not be found. 
'''

原因是theme floder下沒有landscape主題的資料

本地運行時，會使用Hexo 的默認設置和緩存，在

'''
node_modules/hexo-theme-landscape/
'''
目錄下