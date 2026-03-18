---
title: 存取控制的基本概念
---

存取控制 (Access Control) 是將 Principal（主體）與 Resource（資源）連結起來的機制。

意義是**誰可以在什麼條件下存取什麼**的決策。

- Subject (主體)：發起請求的實體 (User, Service, Device)。
- Resource (資源)：被存取的目標 (File, API, Database row, UI Component)。
- Operation (操作)：動作類型 (Read, Write, Execute, Delete)。

## Resource Model

資源模型定義了系統中需要被保護的內容，它決定了權限控制的粒度 (Granularity)。

倘若以資料庫來類比：

- 以「資料庫」為單位：遊戲設定的資料庫，與交易資訊的資料庫是分開的。
- 以「資料表」為單位：同個資料庫底下的用戶，可能看到的資料表是不同的。
- 以「資料列(Row)」為單位：編號為 `ORDER-001` 的訂單。
- 以「資料欄(Column)」為單位：只有主管看得到「毛利 (Profit Margin)」欄位，基層業務能看得到「總金額」。

在現代架構中，通常使用 URN (Uniform Resource Name) 來唯一標識資源。如 AWS 使用 ARN (Amazon Resource Name) ： `urn:myapp:module:resource_type:resource_id`

## Action Model

動作模型定義了 **Pricipal** 可以對 **Resource** 執行什麼操作：

- CRUD：最基礎的對應關係（Create, Read, Update, Delete）
- Business Actions：將多個低階操作封裝為具備業務意義的動作，例如對於訂單，「Approve」比起「Update」更加精確，因為使用者只能修改「交易狀態」欄位，而不能修改「金額」欄位。
- Action Hierarchies：「可寫」權限，通常包含了「可讀」權限，不會只有能寫但不能讀的情況。

## Decision Model

基於 Principal、Resource 與 Action 的輸入，計算出最終結果。

- Allow：明確允許操作。
- Deny：明確禁止操作。
- Not Applicable：當前政策未涵蓋此請求。

### 決策組合策略

- Deny-overrides (拒絕優先)：只要有一個政策說「拒絕」，最終結果就是拒絕。
- Allow-overrides (准許優先)：只要有一個政策說「准許」，就放行。
- First-applicable (首個適用)：按順序執行，第一個得出結果的政策決定一切。

參考 XACML[^1]，存取模型可以使用以下幾個角色來定義：

- PIP (Policy Information Point)：提供決策所需的資料（如資源的屬性）。
- PAP (Policy Administration Point)：管理與編輯這些模型的介面。
- PDP (Policy Decision Point)：執行決策模型的核心引擎。
- PEP (Policy Enforcement Point)：在 API 入口處執行 PDP 的決定。

除此之外，還需要關注**靜態**與**動態**定義，並思考**效能優化**的議題。

在本章節中，意義是讓開發者以「授權規則」的角度思考，而不是侷限在「帳號」的概念，因為帳號容易被當作自然人，但事實上，存取系統的往往不是只有自然人。

[^1]: [XACML](https://docs.oasis-open.org/xacml/3.0/xacml-3.0-core-spec-os-en.html)
