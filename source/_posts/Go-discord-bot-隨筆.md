---
title: Go discord bot 隨筆
date: 2026-04-28 15:47:09
tags:
---
這是AI推薦的學習方法

第一階段：docker run 進 Go 容器，手動學 Go 指令
第二階段：寫 Dockerfile，把剛剛手動做的事自動化
第三階段：docker compose，讓 token、資料夾、重啟策略都管理好

### STEP1
我不想在電腦裡下載go所以首先打開docker
```
docker run --rm -it -v "$PWD":/app -w /app golang:latest bash
```
用 golang:latest 這個 image
把目前資料夾掛到容器的 /app
進入 /app
打開 bash

Q:這串跟直接製作dockerfile有甚麼差別
A:這是臨時開一個 Go 開發容器，像借一台裝好Go的臨時電腦來操作。
dockerfile是定義一個可重複建置的映像檔，像寫一份說明書，每次都照他做出專用電腦。



進入容器了!下一步就是建置Go需要的東西
```
go mod init DC_bot(實際是lab名)

#接著建立cmd/bot/main/go檔案

go run ./cmd/bot
```

`main.go`
```Go
package

import (
    "fmt"
)

func main(){
    fmt.Println("Hello my friend!")
}
```


得到回應Hello my friend!之後進入下一步~

### STEP2
首先製作Dockerfile

```Docker
FROM　golang:latest

WORKDIR /app

COPY go.mod /app

RUN go mod download

COPY cmd ./cmd

CMD ["go","run","./cmd/bot"]
```

接著編譯剛剛的Dockerfile
```
docker build -t ymst_lab_bot .

docker run --rm ymst_lab_bot

```

會出現熟悉的
`Hello my friend!`

### STEP3

讓程式讀取環境變數 推薦寫在docker檔案但我覺得好危險所以沒有這part


## QA
### GO文法問題
mian.go的參數感覺比較好看所以寫成

```Go
func main(){
    fmt.Println(
        "Hello my friend!"
        )
}
```
就吃了syntax error : )

`syntax error: unexpected newline in argument list; possibly missing comma or )`


