# Scope and Claim, Audience

授權模型的討論結束後，接著就可以討論現代身分驗證（Authentication）與授權（Authorization）的核心：OAuth 2.0 與 OpenID Connect (OIDC)。

- Scope 是客戶端向身分提供者請求的權限範圍
  - 粗粒度的權限（例如：read:orders, write:profile）。
- Claim 是關於使用者的具體資訊實體[^1]，以 Key-Value 形式存在於 Token 的 Payload 中
  - 細粒度的數據（例如：email, sub (User ID), given_name）。
- Audience 定義 Token 是發給誰用的
  - 防止 Token 濫用。如果 API 發現 Token 的 aud 不是自己，必須拒絕該請求。

在一個典型的 Json Web Token 中，可以檢閱到以下資訊：

```json
{
  "iss": "https://iam.yhchen.org",          // 發行者 (Issuer)
  "sub": "123456789",                       // 使用者 ID (Claim)
  "aud": "api.yhchen.org",                  // 受眾 (Audience) 
  "email": "user@gmail.com",                // 電子郵件 (Claim)
  "scope": ["openid", "profile", "email"],  // 授權範圍 (Scope)
  "exp": 1736400000                         // 過期時間 (Claim)
}
```

1. Audience check：這個 token 是不是給我的嗎？
2. Token validity：簽名、期限、issuer，這個Token 合法嗎？
3. Scope check：紀載的 scope 中，是否允許這個請求類型？
4. Claim Parser：用 claim 去解析角色、屬性，做其他的授權判斷。

---

在釐清 RBAC/ABAC 的權限模型，也了解了 Scope、Claim 與 Audience 這些通訊協定中的關鍵參數，則剩下的議題很單純：這些資訊到底是被怎麼被傳輸的？

換句話說，我們還沒有提到這些資訊是以什麼形式被攜帶、傳遞、驗證與撤銷的。下個章節將會介紹各種 Token 的種類與特性，說明它們如何在不同 trust boundary 下運作。

[^1]: [OpenID Claim](https://openid.net/specs/openid-connect-core-1_0.html#Claims)
