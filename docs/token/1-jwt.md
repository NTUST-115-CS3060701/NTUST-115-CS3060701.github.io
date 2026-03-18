# Json Web Token

JSON Web Token (JWT) 定義在 RFC 7519，用於實現雙方之間的安全通信。

考慮到一個可以用來表示用戶的 JSON 物件：

```json
{
  "sub": "subject",
  "name": "username"
}
```

並且，可以使用數字簽名的機制，來確保該數據在傳輸過程中沒有被竄改過：服務器使用其私鑰簽署這個 JSON 物件，然後客戶端可以使用服務器的公鑰驗證這個簽名。

## 內容格式

JWT 包含了以下內容：

1. header - 算法和類型
2. payload - 實際數據
3. signature - 涵蓋頭部和載荷的簽名

並使用 `{{header}}.{{payload}}.{{signature}}` 的形式連接起來。考慮到一些字符對於網路傳輸不友好，因此 Header 與 Payload 會先使用 base64 編碼

![jwt](jwt.png)

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

base64編碼後輸出 `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "iat": 1516239022
}
```

base64編碼後輸出 `eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0`

最後使用 `HMAC(base64Url(header) + "." + base64Url(payload), secret)` 輸出 `signature`，最終輸出以下內容為 JWT

```txt
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0.KMUFsIDTnFmyG3nMiGM6H9FNFUROf3wh7SmqJp-QV30
```

## 簽名算法

在前面算法我們使用了 `HMAC`，來進行計算簽章的計算，這是一種**對稱加密**演算法。簽署（Sign）Token 和驗證（Verify）Token 使用的是 同一把私鑰（Private Key）。

另外一種作法是基於**非對稱加密**演算法，IdP 保存私鑰，用來簽署 Token；同時將公鑰給公開，允許任何人下載後驗證 Token 的真實性質。

| 特性     | HS                     | RS、ES、PS 系列                 |
| -------- | ---------------------- | ------------------------------- |
| 類型     | 對稱式 (Symmetric)     | 非對稱式 (Asymmetric)           |
| 金鑰數量 | 1 把                   | 2 把 (Private & Public Key)     |
| 安全性   | 較低（私鑰分發困難）   | 較高（僅簽署方需私鑰）          |
| 速度     | 快                     | 較慢                            |
| 適用場景 | 單一應用程式、內部服務 | 微服務架構、第三方授權 (OAuth2) |

在現代的開發中，我推薦使用橢圓曲線數字簽名算法 (ECDSA) ，能夠建立更緊湊的簽名，且性能更加高效率。

## 驗證方式

1. 使用 `.` 分隔 JWT 的三個部份
2. 使用 base64 解碼 Header 與 Payload
3. 根據 Header 登記的算法，與應用持有的金鑰對簽章進行驗證