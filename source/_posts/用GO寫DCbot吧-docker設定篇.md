---
title: 用GO寫DCbot吧-docker設定篇
date: 2026-04-28 15:47:09
tags:
---
這是AI推薦的學習方法

第一階段：docker run 進 Go 容器，手動學 Go 指令
第二階段：寫 Dockerfile，把剛剛手動做的事自動化
第三階段：docker compose，讓 token、資料夾、重啟策略都管理好

# STEP1
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

# STEP2
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

# STEP3

讓程式讀取環境變數 ~~推薦寫在docker檔案但我覺得好危險所以沒有這part~~

先測試

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	token := os.Getenv("DISCORD_TOKEN")

	if token == "" {
		fmt.Println("DISCORD_TOKEN is empty")
		return
	}

	fmt.Println("DISCORD_TOKEN loaded")
}
```

更改後重新build
```docker
docker build -t ymst_lab_bot .
```
接著,先不給token跑一次
```
docker run --rm ymst_lab_bot
```

這時會看到
```
DISCORD_TOKEN is empty
```

如果在這邊帶上token

```
docker run --rm -e DISCORD_TOKEN=fake_token ymst_lab_bot
```

這時候會得到
```
DISCORD_TOKEN loaded
```

todo:要把token寫在docker file

目前是寫在.env

```
docker run --rm --env-file .env ymst_lab_bot
```



# QA
## GO文法問題
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

## `RUN go build -o /go-docker-app`是甚麼,跟我在cmd打得`docker build`一樣嗎
不一樣

`RUN go build -o /go-docker-app` 目的是把程式碼打包成執行檔並塞進 Image 裡。這時候程式還沒有真正「活過來」執行，它只是變成 Image 的一部分。；而 `docker build` 是在建造映像檔時使用的指令。

## 沒有run go build -o /go-docker-app會發生甚麼

如果你的 Dockerfile 裡面沒有寫 RUN go build -o /go-docker-app，會發生什麼事，取決於你「有沒有用其他方式編譯」或是「你有沒有寫 CMD / ENTRYPOINT」。

狀況一：容器根本不知道要執行什麼（最常見）

如果你只是把 .go 原始碼複製進去，卻沒有編譯它，然後你的 Dockerfile 最後一行寫著類似這樣：
`CMD ["/go-docker-app"]`
當你執行 docker run 時，Docker 會直接崩潰並報錯：

`exec: "/go-docker-app": stat /go-docker-app: no such file or directory`
原因：因為你根本沒有執行 go build，那個叫做 /go-docker-app 的執行檔壓根不存在，Docker 找不到檔案可以執行。


狀況二：每次啟動容器都要「當場重跑編譯」
有些人會想說：「那我不要事先編譯，我讓容器啟動時再執行 go run main.go 總行了吧？」
例如 Dockerfile 最後寫：

`CMD ["go","run","main.go"]`
雖然這樣程式可以跑起來，但會帶來非常嚴重的後果：

1. 啟動速度極慢：每次你執行 docker run，容器都要花時間重新下載依賴、重新編譯程式碼，原本 0.1 秒就能啟動的 Go 程式，變成要等好幾秒。

2. 映像檔肥大：你的 Docker 映像檔裡面必須強迫保留完整的 Go 編譯器（Go SDK）和所有的原始碼，導致 Image 檔案變得非常大。

3. 浪費資源：Docker 的核心精神是「一次 build 好，到處直接執行」。把編譯留在運行期（Run time）完全違背了 Docker 的設計初衷。

## 所以如果我沒有build那行在docker檔案裡 是不是每次都需要手動build then run,但加上去之後就只需要run

不對

結論先幫你劃重點：不論你 Dockerfile 裡面有沒有寫那行 go build，你「手動」要輸入的指令步驟完全是一模一樣的。

你可能會覺得：「咦？既然 Dockerfile 裡幫我寫了 RUN go build，那我不是只要 run 就好嗎？」

這裡有一個巨大的誤區：Dockerfile 裡面的指令，只有在執行 docker build 時才會被觸發。


## `go run ./cmd/bot` VS `CMD ["go", "run", "./cmd/bot"]`

兩個指令都是執行go run ./cmd/bot, 只是它們出現的地方不同

## COPY main.go ./ 跟 COPY cmd ./cmd 的差別
不同在於，我的go程式放在哪 docker file就要copy那裏 cmd也要跑對位置

```
project/
├── Dockerfile
├── go.mod
└── cmd/
    └── bot/
        └── main.go
```
現在的tree是這種樣子
把整個 cmd 資料夾複製到容器的 /app/cmd
然後執行 /app/cmd/bot 裡面的 main.go package

# 採坑大師


## >dqoute是甚麼
是你的code打錯了,根本沒有進去bash,shell還在等你把補完
"沒有閉合


## docker file沒寫go.sum(這邊應該算下一篇的內容)
先前照指示go get也有重build
AI先建議我 
```
go get github.com/bwmarrin/discordgo
go mod tidy
```

`go mod tidy`會整理你的 Go 依賴清單。
它會檢查你的程式碼裡實際 import 了哪些套件，然後自動整理 go.mod 和 go.sum。

但依然沒改善，最後發現問題出在docker file

	COPY go.mod ./
	COPY cmd ./cmd

這邊只複製了go.mod 沒有複製 go.sum 導致找不到

## 大小寫
open 和 Open 不一樣
signal 和 Signal 不一樣

```go
dg.open() //錯誤的
dg.Open() //正確的
```
## 空格
```
token = SSSSS (X)
token=SSSSSSS(O)
```

```
(discordgo的規定)"bot"+ token (X)
"bot " + token (O)  
    ↑需要空白
```