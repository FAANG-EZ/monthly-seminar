---
title: RDBMS 的交易事務
tags: Monthly-Seminar
slideOptions:
  spotlight:
    enabled: true
---
# RDBMS 的交易事務

[![hackmd-github-sync-badge](https://hackmd.io/p98jzc9cToyNJwVAjgsXuw/badge)](https://hackmd.io/p98jzc9cToyNJwVAjgsXuw)


---

# 交易簡介

交易(Transaction)是一個單元工作(unit of work)，包括一個起始點，標註交易開始，這個單元工作包括了數個步驟來完成，交易結束時的動作有兩個，數個步驟全部執行成功，交易才算成功並提交變更(**Commit**)，只有當中有一個失敗，則整個交易宣告失敗並回復所有變更(**Rollback**)。

也就是說交易是為了滿足 **ACID** 中所謂的 **原子性(Atomicity)** 而生。

---

# 交易的種類

----

## Flat Transaction

交易中無其它交易，為一個單元操作。
  
  ```sql
  begin transaction XXX
    ...
  end transaction XXX
  ```

----

## Nested Transaction

在交易中包括其它交易，外部交易要成功，內部交易必須全部成功，應用程式可以重試內部交易，若有外部交易回復，則內部所有交易都要回復。
  
  ```sql
  begin transaction XXX
    begin transaction OOO
      ...
    end transaction OOO

    begin transaction ZZZ
      ...
    end transaction ZZZ
  end transaction XXX
  ```

----

## Chained Transaction

交易不僅有單一提交點，而可以有多個中斷點(break point)，若要回復，則回復動作將至上一個中斷點，而非整個交易被回復。

  ```sql
  begin transaction OOO
    ...
    Commit
    exception
      Rollback
  end transaction OOO

  begin transaction ZZZ
    ...
    Commit
    exception
      Rollback
  end transaction ZZZ
  ```

---

# 隔離層級

隔離性是交易的保證之一，表示交易與交易之間不互相干擾，好像同時間就只有自己的交易存在一樣，隔離性保證的基本方式是在資料庫層面，對資料庫或相關欄位鎖定，在同一時間內只允許一個交易進行更新或讀取。

如果說交易是為了滿足原子性(Atomicity)，那麼隔離可以說就是為了保證**一致性(Consistency)**。

---

# 沒有隔離的世界

在介紹隔離作法前，我們應該要先知道，在交易的世界中若沒有隔離會發生什麼問題

~~防疫大潰堤~~

----

## 更新遺失(Lost Update)

- A 曾 Commit 的變更是否還在？
  1. 交易 A 更新欄位 1
  2. 交易 B 更新欄位 1
  3. 交易 A Commit
  4. 交易 B Rollback
- B Commit 的結果 會被覆蓋？
  1. 交易 A 更新欄位 1
  2. 交易 B 更新欄位 1
  3. 交易 B Commit
  4. 交易 A Commit

----

## 髒讀(Dirty Read)

- B 根據 A 更新的內容做了一些處理，但 A Rollback 了更新內容
  1. 交易 A 更新欄位 1
  2. 交易 B 讀取欄位 1
  3. 交易 A Rollback
  4. 交易 B Commit

----

## 無法重複的讀取(Unrepeatable Read)

- A 前後所看到的資料不同
  1. 交易 A 讀取欄位 1
  2. 交易 B 更新欄位 1
  3. 交易 B Commit
  4. 交易 A 讀取欄位 1

----

## 幻讀(Phantom Read)

- 到底有幾筆資料
  1. 交易 A 進行查詢得到五筆資料
  2. 交易 B 插入一筆資料
  3. 交易 B Commit
  4. 交易 A 進行查詢得到六筆資料

---

# 遵守規定好好隔離

隔離交易的基本方式是鎖定資料庫，但資料庫實務上並不會完全的鎖定，以避免造成嚴重的效能問題。

實務上會根據資料讀寫更新的頻繁性，設定不同的**交易隔離層級**。

----

## Read Uncommited

> 放手前都屬於我的

- 保證：「A 交易已更新但尚未確認的資料，B 交易僅可作讀取動作」
- A 交易在更新但未提交，B 交易的更新會被延後至 A 提交/復原之後
- 至少可避免  **lost update**的問題

----

## Read Commited

> 打扮過後方可見人的精緻女子

- 保證：「交易讀取的資料必須是已確認的資料」
- 讀取的交易不會阻止其它的交易
- 一個未確認的更新交易
  - 阻止其它所有的交易
  - 尚未確定前其他交易只能先操作暫存表格
- 至少可避免 **Dirty Read 以下**問題。

----

## Repeatable Read

> 你一如往常地美好

- 保證：「同一交易內兩次讀取的資料必須相同」
- 讀取交易不會阻止其它讀取的交易
  - 阻止其它寫入的交易
  - 尚未確定前其他交易只能先操作暫存表格
- 至少可避免 **Unrepeatable Read 以下**問題。

----

## Serializable

> 風不平浪不靜心還不安穩
> 一個島鎖住一個人

- 只要有資料不一致的疑慮，交易就必須循序
- A 做讀取交易
  - B 要等 A 結束才能更新
- A 做更新交易
  - B 要等 A 更新完才能讀取
  - B 要等 A 更新完才能更新
- 世界大同、城市美好，輕鬆慢活~~令人嚮往~~

----

## 被隔離的小結

| Isolation Level  | Dirty Read | Unrepeatable Read | Phantom Read |
| :--------------: | :--------: | :---------------: | :----------: |
| Read Uncommitted | YES        | YES               | YES          |
| Read Committed   | NO         | YES               | YES          |
| Repreatable Read | NO         | NO                | YES          |
| Serializable     | NO         | NO                | NO           |

---

# 更加工程化的運作

根據各類 Library 提供的方式不同，主要會分為

- **宣告式(Declarative)**：於檔案定義交易邊界、隔離層級等，無需修改程式原始碼
- 編程式(Programmatical)：直接使用 JDBC 或相關框架的 API

---

# 大量的副程式

AP 與 DB 最大的不同在於封裝了更多的業務行為，細分了大量不同用途的副程式。

> 在茫茫的城市之海中
> 我們再難以一目望見所有交易邊界
> 只得靠著旗號傳播宣示領地的歸屬

----

## PROPAGATION_REQUIRED

- 當前程式必須在一個具有事務的上下文中執行
- 已經有事務在進行，被呼叫端將在該事務中執行
- 否則就重新開啟一個新的事務

----

## PROPAGATION_SUPPORTS

- 當前方法不必須在一個具有事務的上下文中執行
- 已經有事務在進行，被呼叫端將在該事務中執行
- 否則...就算了

----

## PROPAGATION_MANDATORY

- 當前程式必須在一個已存在的事務中執行
- 已經有事務在進行，被呼叫端將在該事務中執行
- 否則...我就讓你跟這個世界說再見

----

## PROPAGATION_REQUIRES_NEW

- 強制自己開啟一個新的事務
- 已經有事務在進行，先掛起該事務，我將另起爐灶

----

## PROPAGATION_NOT_SUPPORTED

- 當前程式不在任何事務中執行
- 已經有事務在進行，先掛起該事務，我無拘無束

----

## PROPAGATION_NEVER

- 當前程式不在任何事務中執行
- 已經有事務在進行，誰都別想他媽的拘束我

----

## PROPAGATION_NESTED

- 毫無反應，就是個 [Nested Transaction](#Nested-Transaction)

----

## 傳播模式一覽

| 交易區間策略  | 說 明                                                                     |
| :-----------: | :-----------------------------------------------------------------------: |
| REQUIRED      | 支援現在的交易，如果沒有的話就建立一個新的交易                           |
| REQUIRES_NEW  | 建立一個新的交易，如果現存一個交易的話就先暫停，並啟始一個新的交易來執行 |
| SUPPORTS      | 支援現在的交易，如果沒有的話就以非交易的方式執行                         |
| MANDATORY     | 方法必須在一個現存的交易中進行，否則丟出例外                             |
| NOT_SUPPORTED | 指出不應在交易中進行，如果有的話就暫停現存的交易                         |
| NEVER         | 指出不應在交易中進行，如果有的話就丟出例外                               |

----

## 說得更明白一點

- 本身不在交易中，而呼叫另一個方法時
  - 在非交易中進行(SUPPORTS、NOT_SUPPORTED、NEVER)
  - 起始新的交易(REQUIRED、REQUIRES_NEW)
  - 丟出例外(MANDATORY)
- 本身在交易中，而呼叫另一個方法時
  - 在客戶端的交易中進行(REQUIRED、SUPPORTS、MANDATORY)
  - 暫停客戶端交易，起始新的交易(REQUIRED_NEW)
  - 暫停客戶端交易，於非交易環境中執行(NOT_SUPPORTED)
  - 丟出例外(NEVER)

---

# Commit 還是 Rollback 這是個問題

離開被宣告事務的程式就將結束事務，而這個事務的 Commit 與 Rollback，就取決於你是正常的 Return 還是收到例外。

----

## 正常回傳，世界和平

----

## 收到例外

- 天哪，是例外(拋
- 大家一起回到那最初始最乾淨的世界開端

---

# 我好像可以處理這個例外

讓我們**謹慎小心的處理它吧**

---

# PROPAGATION_REQUIRED

----

## 我主宰了這場交易

- 不，你不可以
- 即便你優雅地接住了這個例外，你也無法保留你的成果

----

## 在別人的局中搗亂

- 主宰交易的人可以挽回這一切嗎
  - 他如果想嘗試接住這個例外，他只會在最後遭到失敗

---

# PROPAGATION_SUPPORTS

----

## 別傻了

- **在這方面，他與 REQUIRED，並沒什麼不同**
- 順帶一提 **MANDATORY** 那個必須在事務中執行的傢伙也是一樣

---

# PROPAGATION_REQUIRES_NEW

----

## 我主宰了這場交易

- 當例外發生，**誰都無法**留下成果

----

## 在別人的局中搗亂

- 不，你不可以
- 主宰這場交易的人可以嗎
  - 老實說，雖然 **NEW** 本人仍然無法保留成果
  - 但在他之上的其他人只要有良好的解決例外，就不受影響

----

## 靜靜地看著上頭胡鬧

- 嘿，當我新起的事務結束，那我當然也將成果 Commit 了

---

# PROPAGATION_NESTED

----

## 吸收別人的優點

- 巢狀內的一切失敗，都可以不影響外界(跟 **NEW** 一樣友善)
  - 當然，外界的人要好好地接下這個例外

----

## 做出自己的特點-最佳馬仔

- 老闆與長官犯錯，小弟當然也有錯
  - **NEW 會在結束時自己 Commit**
  - **NESTED** 需要等到包裹住自己的其他事務 Commit，才會被一併 Commit

----

## 結論

- NEW
  - 本身發生例外，上層也許仍可以正常處理
  - 上層發生例外，我就是要寫入變更
- NESTED
  - 自己發生例外，上層也許仍可以正常處理
  - 上層發生例外，負責任地一併還原
- 其他
  - 祈禱最好不要發生錯誤
  - 發生錯誤時，至少一切都會被還原

---

# 參考

- [簡介交易](https://openhome.cc/Gossip/EJB3Gossip/TransactionABC.html)
- [簡介隔離層級](https://openhome.cc/Gossip/HibernateGossip/IsolationLevel.html)
- [簡介交易區間](https://openhome.cc/Gossip/EJB3Gossip/TransactionDemarcation.html)
