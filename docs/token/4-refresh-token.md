---
title: 更新權杖
---

更新權杖(**Refresh Token**)是一種長期有效的憑證，用於獲取新的存取權杖，而不用重新進行身份驗證。

## 需求

- 安全考量：Access Token 頻繁在網路上傳輸，被截獲的風險高，所以它的有效期限（TTL）必須很短（例如 5 ~ 15 分鐘）。
- 體驗考量：Access Token 每 15 分鐘過期一次，使用者就得重新輸入帳號密碼登入一次。

因此 Refresh Token 的意義很單純：平常請求不會夾帶，僅在 Access Token 失效時才發出一次交換請求。

且 Refresh Token 通常使用隨機的無意義字串來儲存。

## 安全性

現代系統通常有以下保護措施：

- Refresh Token Rotation：Refresh Token 換取新的 Access Token 時，伺服器會同時發放一個新的 Refresh Token，並把舊的撤銷

- Revocation：使用者登初時，順便撤銷 Refresh Token

