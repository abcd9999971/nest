---
title: vue又騙我學新前端
date: 2025-08-27 16:46:44
tags:
---


vue的特性


`v-if`和`v-show`

> v-show 會在 DOM 渲染中保留該元素；v-show 僅切換了該元素上名為 display 的 CSS 屬性。

> v-show 不支持在 `<template>` 元素上使用，也不能和 v-else 搭配使用。

>v-if 是“真實的”按條件渲染，因為它確保了在切換時，條件區塊內的事件監聽器和子組件都會被銷毀與重建。

>v-if 也是惰性的：如果在初次渲染時條件值為 false，則不會做任何事。條件區塊只有當條件首次變為 true 時才被渲染。

>相比之下，v-show 簡單許多，元素無論初始條件如何，始終會被渲染，只有 CSS display 屬性會被切換。

>總的來說，v-if 有更高的切換開銷，而 v-show 有更高的初始渲染開銷。因此，如果需要頻繁切換，則使用 v-show 較好；如果在運行時綁定條件很少改變，則 v-if 會更合適。

<引用 https://zh-hk.vuejs.org/guide/essentials/conditional.html#v-if-vs-v-show>

