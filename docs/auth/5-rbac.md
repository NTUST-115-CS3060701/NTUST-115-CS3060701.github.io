---
title: 基於角色的存取控制
---

RBAC(Role-Based Access Control) 是最經典、也最廣泛被應用的模型。將權限分配給角色而不是直接分配給用戶，提供了一種靈活且高效的方式來管理系統中的存取權限。

- 主體 (Subject)： 系統的操作**實體**。
- 角色 (Role)： 職能的集合（Ex. 管理員、編輯、會計）。
- 權限 (Permission)： 對特定資源執行的操作（如：讀取檔案、刪除紀錄）。

系統不關心主體能不能進行什麼操作，系統僅需關心角色能進行什麼操作，且該主體是否有對應的角色。

在沒有 RBAC 的年代，我們必須幫每個員工單獨設定權限。想像一下，如果公司有 1000 個人，當系統增加一個新功能時，工程師得手動修改 1000 個人的帳號。

導入 RBAC 後：

- 簡化管理： 只要修改「角色」的權限，所有屬於該角色的人都會自動同步。
- 符合最小權限原則： 可以精準定義每個職位只需要哪些權限，降低安全風險。
- 人員流動方便： 新人入職只要分配「角色」，離職或調職也只需更改角色關聯。

在 IBM 的這篇文章[^1]，RBAC 可以分為四個層次：

- Flat RBAC (基本)： 最簡單的形式，使用者擁有多個角色，角色擁有多個權限。
- Hierarchical RBAC (角色繼承)： 角色有高低之分。例如「Senior Engineer」繼承了「Junior Engineer」的所有權限，並額外增加一些高級權限。
- Constrained RBAC (責任分離)： 加入了限制條件。例如，同一個人不能同時擁有「採購」和「撥款」兩個角色。
- Symmetric RBAC (對稱)： 強度最高的管理方式，支持角色與權限之間的動態檢閱。

RBAC 擅長處理的是穩定、長期存在、語意清楚的權限結構；缺點很明顯：

- 角色數量暴增： 如果業務邏輯太複雜，可能需要為各種情況建立無數個角色。
- 缺乏彈性：無法根據數據內容進行動態判斷。

![RBAC Schema from Wiki](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c2/Role-based_access_control.svg/1280px-Role-based_access_control.svg.png)

一個很重要的概念是「不要依照角色進行檢查，而是檢查權限」

```js
// Bad
if (pricipal.hasRole('product_manager')) {
  deleteProduct(productId);
}

// Good
if (pricipal.hasPermission('product:delete')) {
  deleteProduct(productId);
}
```

[^1]: [How RBAC works](https://www.ibm.com/think/topics/rbac)
