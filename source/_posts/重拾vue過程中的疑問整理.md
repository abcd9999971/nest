---
title: 重拾vue過程中的疑問整理
date: 2026-04-01 17:49:59
tags:
---

又是令人開心的四月，又想來摸點什麼了
重拾了去年10月左右的vue，但每次都這樣動不動就Reset，問ai一堆問題又不記得解決方式這樣也不是問題，所以打算整理一篇

首先，又是卡在安裝這部

1.不想創造新的專案資料夾
一般創造新專案的cmd是
```
npm create vue@latest
```
但這次已經先弄好git repo並且在該資料夾下了，所以直接使用該資料夾
```
npm create vue@latest .
```

使用`. `就可以解決新專案資料夾有兩層的狀況了!

2.關於安裝建議

建議使用vue+vite`npm create vite@latest`，我使用的是單純的vue安裝cmd但是結果還是安裝了vue+vite的樣子

Framework:Vue
Variant:JavaScript
TypeScript太重了，對於小專案不適用
其他選項也都不需要



安裝選項

3.關於部屬建議

這次不想使用git自己的Gitpage，AI推薦使用Cloudflare Pages

推薦（簡單）
Cloudflare Pages（免費、超快）
Vercel（也很簡單）
Netlify

要注意cloudflare一開始就要決定Direct Upload 或是 Git integration，無法更改

4.關於安裝過程中的返回

沒有 喜提ctrl+c套裝

5.我要怎麼知道我的是vite+vue還是普通的vue

**看專案結構**
如果你看到這些檔案：
vite.config.js / vite.config.ts
index.html（在根目錄）
那就是vite+vue(らしい)

看 package.json

如果有：
"dev": "vite",
"build": "vite build"
👉 一定是 Vite

如果是：
"serve": "vue-cli-service serve",
"build": "vue-cli-service build"
👉 就是 Vue CLI

6.我安裝的時候明明是裝 vue 為什麼出現了vite
現在的 Vue 專案，預設就是用 Vite 來建立的
現在 Vue 官方的工具是：

❌ 舊的：Vue CLI（已經慢慢不推薦）
✅ 新的：create-vue（底層就是用 Vite）

👉 官方策略是：

Vue 專案 = Vite + Vue（預設組合）


角色	說明
Vue	框架（寫 UI）
Vite	工具（幫你跑專案、打包）

らしいです

7.使用vue是甚麼意思 難道vue不是已經安裝在本地上嗎

    通过 CDN 使用 Vue​你可以借助 script 标签直接通过 CDN 来使用 Vue：

👉「使用 Vue」是指 讓你的網頁可以用 Vue 的功能來寫

但「怎麼拿到 Vue」有兩種方式：
1.
`<script src="https://unpkg.com/vue@3"></script>`

這代表：
Vue 是從網路載入
不需要 npm / 安裝
適合：簡單頁面、小 demo
2.
npm create vue@latest
現在的方式 本地專案

回答你的問題
Vue不是已經安裝在本地了嗎？
👉 對，但只限你那個專案
不是整台電腦「全域都有 Vue」，而是：

你這個專案裡有 Vue
其他 html 檔案還是不能直接用（除非用 CDN）

8.所以我使用的是全局建構版本嗎
***不是***


👉 你現在用的是：
❌ 不是全局建構版本（global build）
✅ 是模組化版本（module / bundler 版本）