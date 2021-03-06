---
title: Docker 02 ð³ð³
tags: Monthly-Seminar
slideOptions:
  theme: night
  transition: 'fade'
  
---

## Docker 02 ð³ð³
#### Build, Ship, Run.

---

## Docker ä¸å¤§åç´ 
- Image
- Container
- Registry

----

## Image (é¡å)
- ä¸åå¯è®çæ¨¡æ¿ï¼ç±ä¸çµç¨æ¼åµå»º Container çæä»¤çµæ
- ç±ä¸å±¤ä¸å±¤ç Layer å çèæ

----

![](https://i.imgur.com/KDGhvIN.gif)
##### Reference: https://www.youtube.com/watch?v=8vXoMqWgbQQ

----

## Container (å®¹å¨)
- æ ¹æ Image çå®ç¾©åµå»ºçå¯å·è¡åé«
- åå«äºç¨å¼ç¢¼æ¬èº«èææå·è¡æéçä¾è³´é 
- å¨ Image ä¸æ°å¢ä¸åå¯å¯«çå­å²å±¤ï¼çå½é±æçµæå¾æ¸ç©º

----

![](https://i.imgur.com/2DU6Bcf.png)

----

## Registry
- ç¨æ¼å­æ¾ Imagesï¼å¯å¾ Registry pull or push Images

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

1. æå°ææ³è¦ç Docker Image
2. Pull Image
3. ååä¸¦é²å¥ Container
4. å®è£ç¸éç°å¢

----

1. docker search php -f is-official=true
2. docker pull php
3. docker run -ti php /bin/bash
4. maybe apt-get or other command in linux

----

## å¦æéå°éç¨®æå¢...
æ¬¸æ¬¸ä½ é£å image æéº¼å»ºçï¼ææ³è¦èµ·ä¸å°æ¸¬è©¦æ©
æ¬¸æ¬¸ä½ é£å image æéº¼å»ºçï¼ææ³è¦æå°å¦ä¸å°æ©å¨
æ¬¸æ¬¸ä½ é£å image æéº¼å»ºçï¼ææ³è¦ blablabla

----

### æåéè¦ä¸åå·¥å·ä¾è®éäº 87 éå´

----

### Dockerfile

- Dockerfile æ¯ä¸åç´æå­æªï¼åå«äºä¸é£ä¸²çæä»¤
- å·è¡ `docker run` æææ ¹æ Dockerfile ä¸­çæä»¤ build åº image

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

- å¨åä¸åå®¹å¨å§å®è£èªè¨ç°å¢ãè³æåº«ãweb server ... ç­

----

- å¤åå®¹å¨ï¼å¤å dockerfileï¼å¤è¡æä»¤
- è¨­å® portãDB å¸³èå¯ç¢¼ãvolume local file... ç­

----

### æåéè¦ä¸åå·¥å·ä¾è®èªå·±ä¸ç¨å¹¹è ¢äº

----

### docker compose

- docker compose æ¯çºäºå®ç¾©åå±ç¨å¤å®¹å¨æç¨ç¨å¼èéç¼çå·¥å·ï¼å¯ä»¥ä½¿ç¨å®ä¸å½ä»¤ï¼æ§å¶æ´åå°æ¡çææå®¹å¨ã
- å° docker compose ä¿çå¨å°æ¡çæ ¹ç®éççæ¬æ§å¶ï¼å¶ä»äººå¯ä»¥ééç°¡å®çæä»¤ååä¸¦åèå°æ¡ã

----

Demo docker-compose if i am free