---
title: Docker 02 🐳🐳
tags: Monthly-Seminar
slideOptions:
  theme: night
  transition: 'fade'
  
---

## Docker 02 🐳🐳
#### Build, Ship, Run.

---

## Docker 三大元素
- Image
- Container
- Registry

----

## Image (鏡像)
- 一個唯讀的模板，由一組用於創建 Container 的指令組成
- 由一層一層的 Layer 堆疊而成

----

![](https://i.imgur.com/KDGhvIN.gif)
##### Reference: https://www.youtube.com/watch?v=8vXoMqWgbQQ

----

## Container (容器)
- 根據 Image 的定義創建的可執行個體
- 包含了程式碼本身與所有執行所需的依賴項
- 在 Image 上新增一個可寫的存儲層，生命週期結束後清空

----

![](https://i.imgur.com/2DU6Bcf.png)

----

## Registry
- 用於存放 Images，可從 Registry pull or push Images

----

![](https://i.imgur.com/HagD7YT.png)

----

![](https://i.imgur.com/uBiFAZX.png)

----

![](https://i.imgur.com/AnAsqBe.png)

---

## What's Dockerfile?

----

## If we don't have Dockerfile?

----

1. 搜尋我想要的 Docker Image
2. Pull Image
3. 啟動並進入 Container
4. 安裝相關環境

----

1. docker search php -f is-official=true
2. docker pull php
3. docker run -ti php /bin/bash
4. maybe apt-get or other command in linux

----

## 如果遇到這種情境...
欸欸你那個 image 怎麼建的，我想要起一台測試機
欸欸你那個 image 怎麼建的，我想要換到另一台機器
欸欸你那個 image 怎麼建的，我想要 blablabla

----

### 我們需要一個工具來讓這些 87 閉嘴

----

### Dockerfile

- Dockerfile 是一個純文字檔，包含了一連串的指令
- 執行 `docker run` 時會根據 Dockerfile 中的指令 build 出 image

----

```dockerfile=
FROM php:7.4-cli
COPY . /usr/src/myapp
WORKDIR /usr/src/myapp
CMD [ "php", "./your-script.php" ]
```

```dockerfile=
FROM php:7.4-fpm
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd
```

##### Reference: https://hub.docker.com/_/php

----

Demo dockerfile if i am free

---

## What's docker compose?

----

## If we don't have docker compose?

----

- 在同一個容器內安裝語言環境、資料庫、web server ... 等

----

- 多個容器，多個 dockerfile，多行指令
- 設定 port、DB 帳號密碼、volume local file... 等

----

### 我們需要一個工具來讓自己不用幹蠢事

----

### docker compose

- docker compose 是為了定義和共用多容器應用程式而開發的工具，可以使用單一命令，控制整個專案的所有容器。
- 將 docker compose 保留在專案的根目錄的版本控制，其他人可以透過簡單的指令啟動並參與專案。

----

Demo docker-compose if i am free