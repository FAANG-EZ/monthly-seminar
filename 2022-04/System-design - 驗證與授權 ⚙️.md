---
title: System-design - 驗證與授權 ⚙️
tags: Monthly-Seminar
slideOptions:
  theme: night
  transition: 'fade'
---

### System-design - 驗證與授權 ⚙️
#### How to design a auth system

---

## 驗證 vs 授權

----

![](https://i.imgur.com/vW98tlM.png =65%x)

----

![](https://i.imgur.com/sivA5Rb.png)

---

## 驗證 (Authentication)
#### Who are you?

----

## Password
- 最基本的驗證設計，讓使用者擁有一組帳號密碼
- 請千萬要記得做 Hash
    - 確保使用 sha256 以上的演算法
    - 可以的話記得 Salt
        - `(X) Hash(Password)`
        - `(O) Hash(Password+salt)`

----

## 延伸 Hash 相關
![](https://i.imgur.com/EaCsCun.png)

----

## Encode vs Encrypt vs Hash

----

## Encode (編碼)
- 只對資料表達的形式做轉換
- 有特定的轉換規則，只要知道規則即可轉換
- EX: 摩斯密碼、Base64、url encode

----

## Encrypt (加密)
- 需要一組 key，資料會藉由這組 key 做處理
- 分為對稱式加密和非對稱式加密
    - 對稱式加密 -> 加解密都用同一組 key，如 AES
    - 非對稱式加密 -> 分為 public key & private key，如 RSA

----

## Hash (雜湊)
- 將任意大小的 input 經過處理後，轉換為固定長度的 output
- 不可逆，但可能被彩虹表類型的方式攻擊，所以需要 Salt

----

## 延伸結束

----

## 2FA
- 雙因子驗證((two-Factor Authentication))
- EX: 指紋、Google Authenticator

----

## KYC (Know Your Customer)
- 包含一系列的身分證、臉部、文件認證
- 常見於需要實名認證的系統

---

## 授權 (Authorization)
#### What are you allowed to do?

----

## JWT (Json Web Token)

----

## 在沒有 JWT 的時代...
- 我們使用 Session 儲存登入的狀態
- 如果我們想：
    - 設計一個 Stateless 的服務？
    - 水平擴充？

----

## 我們需要一個需求發起端可以提供認證的憑證

----

## What is JWT?

----

![](https://i.imgur.com/hVDn125.png)

----

## JWT 的長相

----

base64(Header).base64(Payload).base64(Signature)

----

```bash
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIi
wibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF
2QT4fwpMeJf36POk6yJV_adQssw5c
```

----

![](https://i.imgur.com/J8fGuQ6.png)

----

## 攜帶方式

----

在 Header 中帶上：
Authorization: Bearer <JWT>

----

## JWT 的優點
- Stateless，方便 scale out
- json 格式，輕巧方便，語言友善

----

## JWT 的缺點
- 洩漏後可能被濫用 -> 可用時效性解決
- 無法如 Session 主動終止 -> 需另外設計黑名單等功能
    
----
    
To Be Continue...