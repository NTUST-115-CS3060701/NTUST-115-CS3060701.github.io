---
title: 什麼是 Token
---

在現代 Web 開發中，Token（權杖）相當於通行證。為了平衡安全性與效能，發展出了幾種不同形式的 Token，它們各有各的應用場景。

## JWT (JSON Web Token) — 自帶資訊的通行證

Token 本身攜帶 claim，資源伺服器只要驗證簽章，就能直接做授權判斷，不需要回查資料庫。這讓它在分散式系統中非常高效，但也意味著一旦簽發，內容在過期前無法撤回或修改。

## Opaque Token (不透明 Token) — 換取資料的號碼牌

資源伺服器必須拿這個 token 去授權伺服器或快取系統換取實際的權限與屬性資料。效能較差，但控制力強，適合需要即時撤銷或集中風險控管的場景。

## Access Token & Refresh Token — 雙權杖機制

Access Token 存活時間短，用於實際存取 API；Refresh Token 存活時間長，只用來換取新的 Access Token。這個機制的目的是降低長效憑證外洩時的風險。
