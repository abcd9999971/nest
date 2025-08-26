---
title: 你該不會沒上database吧
date: 2025-02-10 17:25:07
tags:
---


是這樣的，我大三為了自然群學分沒上Database，然後那科還被當，氣鼠

### 前言

DB與DBMS

- DB 資料庫
    特定資料的集合
- DBMS 資料庫管理系統
    管理資料庫的軟體

###　DB的樣式

- 階層モデル
　- 木構造
- ネットワークモデル
　- インターネットやハイパーテキスト(Web)のデータ構造である。
- リレーションシップモデル
-  オブジェクトモデル

現在は、リレーショナルモデルに基づくデータベース管理システム(RDBMS)が主流。
https://ithelp.ithome.com.tw/articles/10333181

### クエリ(query)←問い合わせ
問い合わせ言語 関係データベースではSQLが標準


- 選択

- 抽出


## SQL

高級的語言，結構化查詢


### 稱呼

- 直向 カラム
- 橫向 レコード


高水準言語 低水準言語

### トランザクション管理

如果沒有好好管理的話可能產生複數購入?

トランザクション DBMS內



A匯款給B 100萬

1. A -100萬
2. B +100萬


若執行完1.但尚未執行2.時系統發生問題，則需要反推1.的操作


## ERモデル

実世界関連モデル

### 弱実体
```
In entity-relationship (ER) diagrams, a weak entity is an entity that cannot be uniquely identified by its own attributes alone; (https://miro.com/diagramming/weak-entity-in-er-diagrams/)
```

Dependentsは弱実体集合
部分キーとidentifying ownerを合わせて、初めて唯一に識別可能

### クラス階層
実体集合の属性は実体集合によって

‐ 制約
overlapping cinstraint 
二つ持ついいかどうか
- 

### 集約(aggregation)

実体集合と関連集合の集まり。

集約機能：最小、最大


### ER図の選択

住所情報の追加
1.属性を追加
2.NDDを追加


4/23
### 關係データベース

CS系はノーベル賞とれない？

なぜ関係モデルを学習する？ もっとも広く使われている

関係
           

                ↓属性attribute（列）
                学生ID| 名前 ｜ 年齢
    組tuple →   0001  | 山田 ｜ 20
                0002  | 佐藤 ｜ 20


関係＝組の集合
 
したがって、組、属性は順序によらない。

| ID   | 名前   | 年齢 |
|------|--------|------|
| 0001 | 山田   | 20   |
| 0002 | 佐藤   | 20   |


| ID   | 名前   | 年齢 |
|------|--------|------|
| 0002 | 佐藤   | 20   |
| 0001 | 山田   | 20   |

| ID   | 年齢 | 名前   |
|------|------|--------|
| 0002 | 20   | 佐藤   |
| 0001 | 20   | 山田   |

三つの表は全て同値

一貫性制約

どのような更新をしても真になければならない

外部キー制約
関係間の

# 5/6 関係代数（関係論理）
- tuple relational calculus(TRC)
    - select *
- Domain relational calculus(DRC)
    - select class

#### tuple relational calculus
    (1) Ratingが7以上のsailorsの名前と年齢を示せ
    {P | ∃S ∊Sailors ( S.rating >7 ∧ P.name = S.sname ∧ P.age = S.age)}

分解して解釈すると：
{P | ... }:
これは「P というタプルの集合。条件を満たす全てのPを集めた集合」という意味です。

∃S ∊ Sailors:
「S は Sailors 関係に属するタプルである」という意味。つまり、Sailorsテーブルの各行をSとして扱っています。

(S.rating > 7 ∧ P.name = S.sname ∧ P.age = S.age):

S.rating > 7: Sailorsの中で rating が 7 を超える行に注目。

P.name = S.sname ∧ P.age = S.age: 条件を満たすSのsnameとageを新しいタプルPのnameとageとして取り出す。



##### Rating > 7 のsailorの名前

    {N|∃I∃T∃A (<I,N,T,A>∊Sailors ∧T>7)} 

    欲しい結果(N)を自由変数にする必要ある

##### Find sailors who’ve reserved all boats

A → B
￢A OR B

sql ＝ 関係論理
関係完備性 最小の制約



#　中間テスト

関係論理の書き方
表現能力



# 院試関連
集約、