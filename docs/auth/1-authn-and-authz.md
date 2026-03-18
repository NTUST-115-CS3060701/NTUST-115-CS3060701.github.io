---
title: Authentication & Authorization
---

身份驗證 (Authentication, **AuthN**) 與 授權 (Authorization, **AuthZ**)這兩者經常被混為一談，但它們在系統安全中扮演著完全不同的角色。

| 特性     | 身份驗證 (AuthN)                | 授權 (AuthZ)                       |
| -------- | ------------------------------- | ---------------------------------- |
| 目的     | 驗證使用者的身份。              | 驗證使用者是否有權執行特定操作。   |
| 發生順序 | 發生在授權之前。                | 發生在驗證成功之後。               |
| 常見方式 | 密碼、生物辨識、OTP、魔術連結。 | RBAC (角色權限)、ABAC (屬性權限)。 |
| 常用技術 | OpenID Connect (OIDC)、SAML。   | OAuth 2.0、Opa、Casbin。           |

## 認證 Authentication

發生在授權之前，系統需要確保「宣稱自己是某人」的使用者真的是本人。身份驗證的本質是「建立信任」。系統必須透過一項或多項因素（Factors），在不可信的網路環境中確認對方的身份。

在**分散式系統**中，我們通常不會讓每個服務都自己處理登入邏輯，而是採用 OAuth2.x 與 OpenID Connect (OIDC)。

1. 使用者嘗試存取應用程式 (RP)。

2. 應用程式將使用者重導向至 身份提供者(Identity Provider, IdP)。

3. 使用者在 IdP 完成登入。

4. IdP 回傳一個 ID Token 給應用程式。

### 身份提供者 Identity Provider

負責管理、維護並驗證使用者身份的「可信賴中央系統」。

> [!TIP]
> 外交部負責簽發護照，海關不需要檢查你的出生證明或身家背景，他們只需要信任並核對那本由外交部核發的護照即可
>
> IdP 的行為如同外交部，海關則是我們旗下的應用或服務

- 使用者管理 (帳號、密碼、姓名、Email 等)。
- 認證服務：提供登入系統確認使用者身份。
- 權杖簽發(Token issuance)：身份確認後，簽發數位憑證（如 JWT、SAML Assertion），提供給下游系統進行辨識。
- 單一登入：使用者登入一次後，就能存取多個相互關聯的系統，無需重複進行認證。

常見的 IdP 包含：

1. 社交身份提供者 (Social IdP)：Google、Facebook、LINE 等，讓使用者利用現有的社交帳號快速註冊/登入。
2. 企業級身份提供者：用於公司內部員工管理，管理員工對內部系統的存取。
3. 自行託管：完全控制資料的團隊，企業內部架設，或是作為自建產品的身份核心。

除了自然人以外，也會管理非自然人的身份：服務、伺服器、Bot。這些情境在實務上稱為 Machine to Machine Authentication

| 特性     | 人對機器 (User-to-Machine) | 機器對機器 (M2M)               |
| -------- | -------------------------- | ------------------------------ |
| 互動     | 有登入介面 (Login UI)      | 無介面，純 API 調用            |
| 驗證因素 | 密碼、MFA、OIDC ID Token   | Client ID/Secret、憑證 (Certs) |
| 會話長度 | 通常較長 (Session)         | 通常較短 (Access Token)        |
| 主要協議 | OIDC (OpenID Connect)      | OAuth 2.0 Client Credentials   |

## 授權 Authorization

授權通常發生在身份驗證成功之後。系統會根據預先定義的策略（Policies），決定是否核准使用者的請求。一個典型的授權請求包含四個要素：

- 主體(Subject): 誰打算存取
- 動作(Resource): 想做什麼
- 資源(Action): 想存取什麼
- 環境(Environment): 在什麼情境下

根據複雜度與需求，業界常用的授權模型有以下三種：

- RBAC：Role-Based Access Control
- ABAC：Attribute-Based Access Control
- ACL：針對特定資源列出名單，例如 Google Docs可以直接填寫「共用編輯者」，把特定的 email 加入到可編輯清單。

### 實作模式

- 內建於代碼 (Middleware)：後端 API 使用 Middleware 進行確認。
- 外部授權引擎( Policy Engine)：OPA (Open Policy Agent) 或 Casbin 這樣的工具。

---

在設計系統時，包含四個核心議題：

1. Token 的安全性：Stateful vs Stateless
2. 精細度 (Granularity)
3. 使用者體驗 (UX)
4. 稽核日誌 (Audit Logs)

這些議題也會是我們打算解決的核心概念。
