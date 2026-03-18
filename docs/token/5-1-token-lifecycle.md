---
title: 權杖的生命週期
---


Token 的生命週期（Lifecycle）描述了一個 Token 從誕生（發放）、使用、更新到最後失效的完整過程。

## Issue phase

當用戶通過身份驗證（例如輸入帳密或透過 IAM Role 認證）後，授權伺服器會執行以下動作：

1. 生成數據：確認用戶的 ID , Role , Scope 等屬性，並注入 issuer , subject , expiry 等訊息
2. 進行簽章
3. 透過偽隨機亂數生成一個 Refresh Token
4. 同時回傳 Access Token 與 Refresh Token

## Verify phase

當用戶端將 Token 放在 HTTP Header 中發送給伺服器時：

1. 使用金鑰驗證簽章，或是查詢 Opaque Token 的訊息
2. 若金鑰格式合法，檢查內容是否合法(是否過期、aud、issuer 等資訊)
3. 檢查其餘授權政策是否通過

## Refresh phase (Optional)

Access Token 到期，但用戶的 Session 尚未結束時：

1. 用戶端收到 `401 Unauthorized` 錯誤。
2. 使用 Refresh Token 去授權伺服器換取新的 Access Token
3. 作廢舊的 Refresh Token，並給予一個新的 Refresh Token

## Revocation phase

Token 失去效力的情境大概可以分成：

1. Expiration：時間到就過期
2. Revocation：用戶點擊登出，刪除前端的 AccessToken，並把 Access Token 加入黑名單，同時資料庫移除 Refresh Token
3. Rotation：如果發生了一些安全性事件，例如授權中心撤銷原本的KeyPair，舊私鑰簽發的所有 Token 會集體失效；或是用戶更換了密碼後，被要求重新登入，原本的 Token 也會失效。


