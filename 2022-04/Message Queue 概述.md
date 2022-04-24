---
tags: Monthly-Seminar
---

# Message Queue 概述

===

![簡單示意](https://i.imgur.com/JJZLIui.png)

## 前言

MQ 訊息佇列。首先我們將簡單聊一下 MQ 的前世今生，再簡單介紹幾種常用訊息佇列協議

- MQTT
- XMPP
- Stomp
- AMQPOpenWire

然後在這個基礎上介紹幾種 MQ 產品以及他們運作模式的差異比較。

## 訊息處理

- **訊息**：資訊的載體
- **訊息協議**：按照一種統一的格式描述訊息，這種統一的格式稱之為訊息協議。

在所謂的軟體開發中很常會有一個情境是需要在不同的服務間傳遞、接收訊息，
這種訊息的傳遞接收，可以是 RPC、RestfulAPI、WebSocket、Kafka 等林林總總五花八門。

最主要可以區分為兩類：

- 即時訊息通訊：
  - RPC
  - RestfulAPI
  - WebSocket
- 延遲訊息通訊：
  - 訊息從某一端發出後，首先進入一個容器進行臨時儲存，當達到某種條件後，再由這個容器傳送給另一端。 這個容器的一種具體實現就是**訊息佇列**。
    - ActiveMQ
    - RabbitMQ
    - Kafka

## 中介層(Middleware)

### 特點

- 中介層是軟體，而且是一大類具有不同功能的軟體，而不是一種具備某具體功能的軟體；
- 中介層是伴隨著網路而應運而生的；
- 中介層是應用軟體與系統軟體之間的「中介件」「介中軟體」，起到支撐與連接作用；
- 中介層不是開發工具，但它具有開發、管理和部署的功能；
- 中介層是為應用軟體服務的，應用軟體是為終端使用者服務的，終端使用者並不直接使用中介層；
- 中介層不是大眾消費類軟體產品，除非是一個行業內的專業人士，普通消費者是不會接觸中介層的；在基礎軟體（作業系統、資料庫、中介層）中，中介層是最不為人所了解的；

### 作用

- 整合執行環境：中介軟體能夠封鎖、彌合底層執行環境中的差異性與異構性，確保整個系統在異構平台間能夠穩定執行；
- 提高開發效率：中介軟體能夠提高開發效率，大幅縮短開發周期；
- 軟體品質：中介軟體能夠明晰系統內的不同層級，對接不同的可插拔方式，從而保障軟體的品質；
- 節約成本：中介軟體可大幅節省應用系統內的系統資源，並減少執行成本。

### 訂單系統舉例

原始設計：

![原始系統](https://i.imgur.com/2vY2UgB.png)

採用中介層：

![採用中介層](https://i.imgur.com/dyoYIsK.png)

## MQ

![MQ 架構](https://i.imgur.com/pYYM78k.png)

### 使用 MQ 的主要目的

- 解耦

解耦前

![解耦前](https://i.imgur.com/onAArG4.png)

解耦後

![解耦後](https://i.imgur.com/PKw4iae.png)

- 異步處理

異步處理前

![異步處理前](https://i.imgur.com/TJHzDAh.png)

異步處理前

![異步處理前](https://i.imgur.com/c0M5Vt4.png)

- 削峰填谷

削峰前

![削峰前](https://i.imgur.com/tXFwQZx.png)

削峰後

![削峰後](https://i.imgur.com/2WE6Bvy.png)

### MQ 分類

- 推模式(Push)：由 MQ 主體**主動**將訊息推給消費者
  - 訊息的傳遞掌控在產出並發送的端口(生產者)
  - 相對及時
  - 主體無法確定此一訊息是否確實推送成功
  - 無法確定消費者的負載
- 拉模式(Pull)：由消費者自主向主體拉取訊息
  - 消費者可針對自己當前負載狀況決定是否取得訊息(削峰填谷)
  - 未必能夠及時處理訊息
  - 訊息可能堆積在主體
- 以拉模式為主，兼有推模式

### 協議

![協議](https://i.imgur.com/pEPCZOV.png)

從上圖可以看到，某一種訊息通訊軟體（或者叫做程式庫）的實現都建立在"協議"基礎上：RMI 程式庫建立在 RMI 協議上（RMI 協議是 JAVA 規範協議的一部分） ，屬於一種"即時訊息通訊"；RabbitMQ 和 Qpid 訊息通訊軟體的設計依據是 AMQP 協議，屬於一種"延遲訊息通訊"。

雖然訊息協議存在"私有協議"和"開放協議"之分（是否向行業開放訊息規範文件、是否允許某個組織更改協議），雖然某一個軟體（程式庫）不一定只支援一種協議（例如ActiveMQ實現了多種訊息協議），雖然某一種協議也不一定只有一種軟體（程式庫）實現（例如能夠支援webservice協議的程式庫就有Codehaus XFire、Apache CXF、Jboss RESTEasy等），但是這並不影響 **"某一種訊息通訊軟體（或者叫做程式庫）的實現都建立在"協議"基礎上"的概念，反而是這個基本概念加強了**。

### 比較

| 對比方向 | 概要                                                         |
| -------- | ------------------------------------------------------------ |
| 吞吐量   | 萬級：ActiveMQ、RabbitMQ（ActiveMQ 的性能最差）、十萬級甚至是百萬級：RocketMQ 和 Kafka。 |
| 可用性   | 都可以實現高可用性。ActiveMQ 和 RabbitMQ 基于主從架構實現。RocketMQ 基於分散式架構、Kafka 也是分散式的，一個訊息多個副本，少數機器當機，並不會丟失訊息。 |
| 時效性   | RabbitMQ 基於 erlang 開發，所以併發能力較強，性能較佳、延時很低達到微秒級。其他三个都是 ms 級。 |
| 支援功能 | 除了 Kafka，其他產品的功能都較為完整，Kafka 功能較為簡單，主要支援簡單的 MQ 功能，在大數據領域的實時計算與 Log 採集被廣泛使用。 |
| 訊息丟失 | ActiveMQ 和 RabbitMQ 丢失的可能性非常低，RocketMQ 和 Kafka 理論上不會丟失。 |

### 實做 MQ 架構最需要注意的問題

- 訊息的可靠投遞：確保訊息不會丟失
  - 訊息落地
  - 超時，重傳，確認
- 訊息冪等性：使用同樣的條件，一次請求和重複多次請求對系統資源的影響是一致的。
  - 分散式系統設計中，設計必須考慮，底層重試時(並不一定是應用層發起的，有時候是TCP 層，有時候是鏈接池層，有時候是框架層，這些重試，並不是應用層能夠控制的)，系統不會異常，數據不會不一致。
  - 關鍵是訊息去重複
    - 生產者產出的訊息到 MQ 裡重複時的辨識排除
    - 消費者取得訊息時如何識別這條訊息是否已有被使用過

## Kafka

Kafka 是基於 ZooKeeper 協調的一個分散式訊息隊列系統，可以以極高的效率以及可用性確保訊息傳達。

具備以下優點：

- 高吞吐量、低延遲：每秒可以處理數十萬條訊息，延遲最低只需要幾 ms 即可。
- 可擴充套件性：透過向 Zookeeper 註冊，各個叢集可隨時建立或拔除。
- 永續性、可靠性：訊息會被持久化到硬碟中，並且備份防止丟失。
- 容錯性：多節點故允許其中節點失敗
- 高併發：支援數千個端點同時讀寫

也因此分散式系統多數都會搭配 Kafka 來充作多台伺服器之間分配服務請求或是 Log 收集的工具。

### 架構

![Kafka 集群](https://i.imgur.com/pzVFspb.png)

Kafka 與 Zookeeper 由以下數個角色組成：

![角色分類](https://i.imgur.com/NI8sqDk.png)

- Brokers(Kafka 伺服器)：就是個伺服器，接收從 Producer 來的訊息，允許 Consumer 讀取訊息。
- Producers(生產者)：可以產生出各種訊息 Push 到 Brokers 上。
- Consumers(消費者)：從 Brokers 中將訊息 Pull 下來。
  - 原則上一個訊息從 Producer 送出至 Brokers 後，系統中所有的 Consumer 都會 Pull 到該條訊息(併發處理)。
  - Group：也可以由 Group 將訊息 Pull 下來，並由 Group 中的其中一個 Consumer 處理(不同 Group 負責不同事務，Group 中的 Consumer 負責同樣的工作，透過在 Group 中新增多台Consummer可以將工作量分流，並確保只有單一台Consummer工作)。
- Zookeeper：協調及控制 Brokers、Consumer、Group 之間的訊息傳遞。

在 Kafka 中訊息的定義如下：

- Topic：訊息的分類，Producer 可以控制將訊息送至哪些 Topic，Consumer 也可以決定要訂閱哪些 Topic。
- Partition：每個 Topic 可以分成數個 Partition，為的是將 Topic 訊息分散送至不同 Broker，由不同 Consumer Group 吃下不同 Partition 的訊息，由此達成高吞吐量的目標。
    如圖所示，同一 Topic 可能可以分成不同 Partition 其中儲存不同的訊息，Consumer Group 來 Pull 訊息時，這些 Partition 訊息則會依隊列分配出去。
    故一般而言我們可透過控制 Partition 及 Group 中 Consumer 的數量來進行負載平衡，值得注意的是若 Partition 高於 Consumer 數則可能會有 Consumer 需要處理一個以上 Partition 的訊息，而 Consumer 數若高於 Partition 則代表有 Consumer 會永遠接收不到訊息。

![Partitions 示意](https://i.imgur.com/nZMS4Fx.png)

### 採用 Kafka 應該如何確保訊息不丟失

- Producers 丟失
  - Producer 在送訊息時一般是異步模式，可以改為採用同步模式傳送。(不推薦)
  - 維持異步生產訊息，但設定 callback function 再取得上次傳送結果後判斷是否重送。
  - Kafka 內建自動重送設定(Producer 的 retries)
- Consumers 丟失
  - Consumer 從 partitions 消費訊息時，會自動設定一個 offset(偏移量)在該 partition，表示佇列中訊息已經消費到此。
  - 可以改為不要自動提交 offset，改為手動，由 Consumer 確定處理完畢後才送交 offset(只要沒人宣告處理完畢 offset 就無法前進)，但帶來的問題是若 Consumer 實質處理完，但要提交 offset 時 shutdown，該筆訊息可能會被當作沒人處理，改由新接手的 Consumer 消費(訊息重複)。

### 採用 Kafka 應該如何確保訊息冪等

- Consumer 已經消費卻沒成功提交 offset
  - Consumer 端的業務處理應自行注意訊息冪等。