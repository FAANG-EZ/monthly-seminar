---
title: Docker 🐳
tags: Monthly-Seminar
slideOptions:
  theme: night
  transition: 'fade'
  
---

## Docker 🐳
#### Build and Ship any Application Anywhere

---

## 簡介
Docker 是一個開源、標準化、虛擬化的軟體工具，使用者可以透過 API 操作指令來建置、執行、部署容器(Container)。

Docker 想解決的是傳統系統中==環境難以保持一致性==與==難以遷移==，透過容器隔離出獨立的執行空間，並將應用程式與執行環境共同打包的方式，解決「**在我的機器上沒問題啊**」的問題。

----

![](https://i.imgur.com/llo6WES.png)

---

## 在沒有 Docker 的蠻荒時代
我們的 Infrastructure 是怎麼運作的？

----

![](https://i.imgur.com/qyMuYXV.png)

----

如果神說想遷移，我需要考慮：
- Backend OS 的版本與套件？語言環境的設定？
    - EX: Ubuntu (apt-get XXX)? PHP5? PHP7?
- Web Server 上的設定檔與套件？
    - EX: Nginx (nginx.conf and nginx module)
- Database 的安裝版本？
    - EX: MySQL 5.7? MySQL 8.0?
- 加班會按照勞基法給薪給假嗎？
    - 週六週日哪一天是例日？哪一天是假日？給不給特休？

----

## Docker 的文明之火
容器可以拯救我們的加班時間嗎？

----

![](https://i.imgur.com/NkSdWSI.png)

----

- 將個別的服務包在容器裡面，形成各自獨立的執行個體，彼此之間藉由 docker network 互相溝通
- 將所有的版本、套件、設定檔都封裝在容器中，實現良好的可遷移性，替未來的可擴充的架構、微服務、Serverless 等打下基礎
- 所有容器皆可透過指令(或者是 Dockerfile or docker compose) 來操作，替後續自動化的持續整合與持續部署 (CI/CD) 打下基礎

---

## Docker vs Virtual Machines (VMs)
Docker 和 VM 都是虛擬化的技術，差別在於對 OS 的使用方式不同。

----

![](https://i.imgur.com/3bomg57.png)

VM 需要藉由 Hypervisor 來模擬出軟體、韌體、硬體，並且在原有的 OS (Host OS) 上實際安裝一套新的作業系統 (Guest OS)。

----

![](https://i.imgur.com/ynvL53C.png)

Docker 則是直接運行在原有的 OS (Host OS) 上，Container 中的環境 Base on kernel of Host OS，相較於 VM 省去了等待 Guest OS 開機的時間，所以更輕量化，執行與啟動的時間也更快。