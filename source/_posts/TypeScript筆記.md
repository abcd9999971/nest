---
title: TypeScript筆記
date: 2025-03-07 13:04:59
tags:
---
有趣的歷史小故事
https://typescriptbook.jp/overview/before-typescript

```bash
const y = 1;
y = 2
>> TypeError: Assignment to constant variable.
```
用const創造的變數不可以被再帶入!
但是可變物體的內容可以更改
```typescript
const obj = { a: 1 };
obj.a = 2;
```

## var的缺點

### 會動到全域設定
var定義全域變數時，實際上會存到window的屬性下面
例:
```typescript
var innerWidth = 10;
console.log(window.innerWidth);
>> 10
```
(例子是這樣寫但我在online ide跑是沒被覆寫)

### 変数巻き上げ

```typescript
console.log(bar); // undefined
console.log(foo); // ReferenceError
var bar = 1;
let foo = 2; // End of TDZ (for foo)
```
var變數會一開始就確保儲存空間並帶入undefined，所以一開始console.log(bar)不會出現error，let定義的foo也會確保儲存空間，但在出現前會進入TDZ(Temporal Dead Zone)，不是以位置順序定義，而是以實行的時間順序。

```typescript
function output() {
  var x = 1;
  {
    console.log(x); // ReferenceError
    let x = 2;
  }
}
output();
```
上面這個例子解釋了let的変数巻き上げ，只有var的話他會跑出undefined，但因為x的變數領域已經被確保，並且進入TDZ所以console.log會是referenceError

### 定義領域

```typescript
function print() {
  var x = 1;
  if (true) {
    var x = 2;
    console.log(x);
    >>2
  }
  console.log(x);
  >>2
}
```
var在{}內的定義會覆蓋全域函數。但是{}內新定義的var物體在{}外無法存取(?)

```typescript
{
    var x = 1;
}
console.log(x);
```
依照上面的理論這應該會error，但ide實際上有給出x=1的答案，computer science...

let、const是block scope，例如{}內的定義只會影響{}內的物體。

## カタカタカタカタ

### 基本アノテーション(型註釋)
`:TypeAnnotation`構文
```
var num: number = 123;
function identity(num: number): number {
    return num;
}
```
### プリミティブ型(primitive)
- ミュータブル(immutable)  
    不可變
- プロパティを持たない 
    基本上不會有屬性，例如
    ```
    null.toString(); // エラーになる
    ```
    但是也存在文字列，數值這種例外
    ```
    "name".length; // 4
    ```
プリミティブ型は次の7つがあります。

1. boolean型(論理型): trueまたはfalseの真偽値。
2. number型(数値型): 0や0.1のような数値。
3. string型(文字列型): "Hello World"のような文字列。
4. undefined型: 値が未定義であることを表す型。
5. null型: 値がないことを表す型。
6. symbol型(シンボル型): 一意で不変の値。
7. bigint型(長整数型): 9007199254740992nのようなnumber型では扱えない大きな整数型。
### オブジェクト型
上述七種以外都是

### number型

- 小數可省略0  
    ```typescript
    5.0 === 5.  
    0.1 === .1
    ```

- 支持2進数、8進数、16進数的寫法
    ```typescript
    0b1010 // 2進数
    0o755 // 8進数
    0xfff // 16進数
    ```

- 數字的区切り
    ```typescript
    100_000_000 // 1億
    ```


### 配列(Arrays)
[]を有効な型アノテーションに後置
```typescript
var boolArray: boolean[];
boolArray = [true, false];    

let array: number[];
let array: Array<number>; //這兩種都是一樣的
```

#### loop array的方法
```typescript
const char = ["a","b","c"];
```

- for
    ```typescript
    for(let i = 0,i < char.length , i++){
        console.log(char[i],i);
    }
    ```


- for-of
    ```typescript
    const char = ["a","b","c"];
    for(const value of char){
        console.log(value);
    }
    ```

- for-in(不推薦)
    ```typescript
    for(const value in char){
        console.log(char[value]);
    }
    ```


- break
    ```typescript
    for (let i = 0; i < char.length; i++) {
    console.log(char[i]);
    if (char[i] === "b") {
        break;
    }
    }
    ```
- continue
    ```typescript
    for (let i = 0; i < char.length; i++) {
    if (char[i] === "a") {
        continue;
    }
    console.log(char[i]);
    // b c が順に出力される
    }
    ```
#### 處理要素的方法
- forEach
    forEachには戻り値がありません。for文などと異なり、breakやcontinueは使えません。
    ```typescript
    const arr = ["a", "b", "c"];
    arr.forEach((value, i) => {
      console.log(value, i);
      // a 0
      // b 1
      // c 2 の順で出力される
    });
    ```
- map
    ```typescript
    const arr = ["a", "b", "c"];
    const arr2 = arr.map((value) => value + value);
    console.log(arr2);
    [ 'aa', 'bb', 'cc' ]
    ```
    mapメソッドも要素ごとにコールバック関数を実行します。コールバック関数の戻り値が、mapには戻り値になります。配列要素の値を加工して、別の配列を作るときに便利です。mapではbreakやcontinueは使えません。

### 配列のスプレッド構文「...」(spread syntax)

不使用的時候
```typescript
const arr = [1, 2, 3];
const arr2 = [];
for (const item of arr) {
  arr2.push(item);
}
arr2.push(4);
```
使用的時候
```typescript
const arr = [1,2,3];
const arr2 = [...arr,4];
const arr2 = [0, ...arr, 4];
```
### 配列のコピー
浅いコピー
```typescript
const arr = [1, 2, 3];
const backup = [...arr];
arr.push(4); // 変更を加える
console.log(arr);
>> [1, 2, 3, 4]
console.log(backup); // コピーには影響なし
>> [1, 2, 3]
```
浅いコピー只能複製第一層(プリミティブ型)。
配列の中に配列が入っている場合は、2層目より深くにある配列は、元の配列のものと値を共有します。

```typescript
const arr = [1, [2, 3]];
const backup = [...arr];
arr[1].push(4);
console.log(arr[1]);
>> [2, 3, 4] 
console.log(backup[1]); // 変更の影響あり
>> [2, 3, 4] 
```



### インターフェース(Interfaces)
複數型共用單一名字
```typescript
interface people {
    name:string;
    age:number;
}

let a : people;
a = {
    name:"john",
    age:23
}
console.log(a);
>>{
  "name": "john",
  "age": 23
} 
```

構造:{/* Structure */}を使って_インライン_で必要なものにアノテーションを付けることができます
```typescript
let people: {
    name:string;
    age:number;
};
```
只用一次的時候可以快速提供。
