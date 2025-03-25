---
title: django(聽起來很不妙)
date: 2025-03-18 10:13:55
tags:
---


### 前置

總之先使用萬能的venv!

```
python -m venv venv
source venv/bin/activate //啟動!
```

然後再複習一下git連心...新repository的方法，
git init!
git remote add origin~

第一次push要upstream!


在django/globol_setting裡面追加
```
INSTALLED_APPS = [
    'rest_framework'
]
```
(官網你嘛好歹寫一下)

在venv裡面用pip list確認所下的包一覽


### 創建project
```
django-admin startproject pj名字
```
會自動在目前目錄下方創建資料夾，使用cd進入

### 創建app
```
python manage.py startapp app名字
```


### 第一章 serializer

serializer=序列化
要了解這個東西，直接實作看看吧

##### 第零步 model定義



首先導入需要的庫。
```
from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_styles
```
接著定義全域變數。
```
LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted([(item, item) for item in get_all_styles()])
```
- `LEXERS`：從 Pygments（用於程式碼語法高亮的庫）中取得所有可用的語法解析器（lexers）。
- `LANGUAGE_CHOICES`：提取 Pygments 支援的語言名稱，形成一個選擇列表，例如 ('python', 'Python')。
- `STYLE_CHOICES`：獲取所有可用的語法高亮風格，形成一個選擇列表，例如 ('friendly', 'friendly')。

大老師開示
>  `LEXERS` 是從 pygments.lexers 中獲得的一個語言詞典列表。每個項目 item 是一個元組，item[1] 是語言的別名列表，而 item[0] 是語言的名稱。item[1][0] 取的是該語言的第一個別名。`LANGUAGE_CHOICES`的兩個相同的 item 值是為了滿足 Django choices 欄位的要求，choices 必須是由二元組組成的列表，其中第一個值是顯示的選項，而第二個值是儲存在數據庫中的實際值。


接著是最重要的模型。
```
class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    class Meta:
        ordering = ['created']
```
這段code定義了snippet物件的性質/屬性，拿第一行舉例

```
 created = models.DateTimeField(auto_now_add=True)
```
`created`屬性名，使用了 `models.DateTimeField`，這是一種用來儲存日期和時間的欄位。 `auto_now_add=True` 的設置表示創建 Snippet 物件時，`created` 欄位會自動填充當前的日期和時間，且該欄位在之後不會被修改。

其他行同理，接著我們看看最下面的`class Meta`:
`class Meta` 中的 ordering = ['created'] 表示 Snippet 物件在查詢時會根據 `created` 欄位進行排序，默認是按創建時間從早到晚排序。

大老師開示:
> 這段程式碼中的 class Meta 並不是一個嵌套的類（class inside a class）。它是 Django 模型中的一種特殊內部類別，稱為 Meta 類。雖然它看起來像是 Snippet 類的子類，但其實它並不是一個嵌套的類。Meta 類在 Django 中有特定的作用，用來定義與模型相關的元數據（metadata），例如排序規則、表名、唯一約束等。

最後，創建和應用數據庫遷移。
```
python manage.py makemigrations snippets
python manage.py migrate snippets
```
1. `makemigration`

    是生成 snippets 應用的遷移文件。
    
    當你在模型（例如 Snippet）中進行更改時，需要創建遷移文件。這些遷移文件記錄了數據庫結構的更改，例如新增、修改或刪除模型欄位。

    snippets 是你的應用名稱，這個參數告訴 Django 只為 snippets 應用生成遷移文件。
    如果一切正常，這條命令會在 snippets/migrations/ 目錄下創建一個新的遷移文件，通常是按時間戳命名的文件，例如 0001_initial.py。

2. `migrate`

    作用是應用遷移文件到數據庫，即實際執行模型更改並更新數據庫結構。這樣，Django 會根據之前創建的遷移文件來創建或修改數據庫表結構。

    同樣，snippets 參數告訴 Django 只對 snippets 應用應用遷移。
    如果遷移成功，數據庫會根據 makemigrations 生成的遷移文件進行更新，並創建相應的表和欄位。

##### 第一步
定義完snippet之後，來進入這章的正題，序列化serializer，其實序列化就是把data轉換成其他格式，例如json。

了解了這個定義就開始實做吧!
先導入需要的庫，這邊我們從rest_framework導入寫好的serializers
接著從剛寫的models文件裡導入snippet模型的定義跟兩個全域變數。


```
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES
``

class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        """
        Create and return a new `Snippet` instance, given the validated data.
        """
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """
        Update and return an existing `Snippet` instance, given the validated data.
        """
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance




```
serializer = SnippetSerializer(Snippet.objects.all(), many=True)


>>> serializer.data
[{'id': 1, 'title': '', 'code': 'foo = "bar"\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}, {'id': 2, 'title': '', 'code': 'print("Hello,world")\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}, {'id': 3, 'title': '', 'code': 'foo = "bar"\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}, {'id': 4, 'title': '', 'code': 'print "hello"\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}, {'id': 5, 'title': '', 'code': 'print "hello"', 'linenos': False, 'language': 'python', 'style': 'friendly'}]


serializer 

# SnippetSerializer(<QuerySet [<Snippet: Snippet object (1)>, <Snippet: Snippet object (2)>, <Snippet: Snippet object (3)>, <Snippet: Snippet object (4)>, <Snippet: Snippet object (5)>]>, many=True):
    id = IntegerField(read_only=True)
    title = CharField(allow_blank=True, max_length=100, required=False)
    code = CharField(style={'base_template': 'textarea.html'})
    linenos = BooleanField(required=False)
    language = ChoiceField(choices=[('abap', 'ABAP'), ('abnf', 'ABNF'), ('actionscript', 'ActionScript'), ('actionscript3', 'ActionScript 3'), ('ada', 'Ada'), ('adl', 'ADL'), 

```


