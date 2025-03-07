---
title: sql note
date: 2025-02-12 15:11:33
tags:
---


##### not演算
`NOT` 或 `!=`



##### 末尾範圍
`WHERE status_cd ~ '[1-9]$'`
如果只有數字或許可以
`WHERE  status_cd LIKE '%0'`

正則表現式
`WHERE status_cd ~ '^[A-F]' AND status_cd ~ '[1-9]$' `
連接時要寫整句`status_cd ~ '^[A-F]'` 不能從中連接
或是使用
`WHERE status_cd ~ '^[A-F].*[1-9]$' `


##### 電話番号
例題給的問題是要找出XXX-XXX-XXXX這樣的號碼
而參考答案給的是
SELECT * FROM store WHERE tel_no ~ '^[0-9]{3}-[0-9]{3}-[0-9]{4}$';
但實際實行卻沒有hit任何東西

問題排除!
- 先確保有電話號碼
    ```sql
    %%sql
    SELECT DISTINCT tel_no
    FROM store
    LIMIT 5;

    0	03-0123-4023
    1	043-123-4003
    2	045-123-4031
    3	046-123-4035
    4	045-123-4046

    ```
- 接著只篩前面
    ```sql
    %%sql
    SELECT *
    FROM store 
    WHERE tel_no ~ '^[0-9]{3}-' LIMIT 5;

    0	03-0123-4029
    1	03-0123-4014
    2	03-0123-4028
    3	03-0123-4018
    4	03-0123-4020
    ```
    絶対！おかしいい！！！！

- 改變篩選的次數
    ```sql
    %%sql
    SELECT *
    FROM store 
    WHERE tel_no ~ '^[0-9]{4}-' LIMIT 5;
    WHERE tel_no ~ '^[0-9]{2}-' LIMIT 5;
    ```
    都沒有hit

    - 土法煉鋼
    ```sql
    %%sql
    SELECT *
    FROM store 
    WHERE tel_no ~'^[0-9][0-9][0-9]-'
    ```

    有成功!
    ```bash
    0	043-123-4003
    1	042-123-4008
    2	045-123-4032
    3	045-123-4043
    4	042-123-4045
    5	045-123-4046
    6	045-123-4053
    7	042-123-4030
    8	045-123-4042
    9	045-123-4034
    ```

##### HAVING
針對group之後的各項目