---
title: TypeScript筆記-函數與其他
date: 2025-03-07 17:13:14
tags:
---


### アロー関数 (arrow function)

```
(引数) => {
  // 処理内容
};
```
#### 命名
アロー関数是式。式=評価結果代入數值。
給アロー関数命名的方式是変数代入

```typescript
const increment = function (n) {
  return n + 1;
};

// 參考格式
const 名 = (引数) => {
    // 処理内容
}
// 可寫成
const increment = (n) =>
{
   return n+1
}

```
#### 省略
只有一個引數的時候可以省略括號，沒有引數的時候不行!
```typescript
const increment = n => { /* ... */ };

const getOne = () => {
  return 1;
};
```

評價的式只有一個的時候，{}跟return可省略。
```typescript
const increment = n => n+1


```

省略形は簡潔文体(concise body)
非省略形はブロック文体(block body)
簡潔文體的
```typescript
(n) => { foo: n + 1 }; // 誤り
(n) => ({ foo: n + 1 }); // 正しい
```