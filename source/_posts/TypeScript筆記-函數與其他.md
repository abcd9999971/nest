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

### 戻り値ない関数

在JavaScript，返回值會自動變成undefined
```javascript
function fn() {
  // 戻り値のない関数
}
const result = fn();
console.log(result); // undefined
```


在TypeScript，使用void定義這些函數
```typescript
function fn(): void {
  // 戻り値のない関数
}
```


  在 TypeScript 中，`void` 和 `undefined` 是不同的型別：

  `undefined` 型別可以賦值給 `void` 型別。但是 `void` 型別不能賦值給 `undefined` 型別。

  這可以理解為：`void` 是 `undefined` 的「上位型別」，意味著 `undefined` 是 `void` 的一個「子類型」，可以被賦給 `void` 變數，但反過來不行。



### promise

request1->request2->request3とする。

function request1 (callbask) :{
  setTime(()->

  )

}


// 非同期でAPIにリクエストを投げて値を取得する処理
function request1() {
  return new Promise(()=>{}){
    
  }
}



### 汎用函數(Generics)
類似超級class?
- reverse<T>(items: T[]): T[]：這是使用了 泛型 的函數，可以處理任何類型的陣列，並且保持類型的安全性。T 是一個佔位符，它會在函數使用時被具體的類型替代。
- reverse(items: T[]): T[]：這種寫法會有錯誤，因為 T 必須在函數外部或是作為泛型參數來定義，這樣寫會讓 TypeScript 無法知道 T 是什麼類型。