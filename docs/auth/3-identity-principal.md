---
title: 可驗證的主體
---

主體 (Principal) 是一個核心單位。簡單來說，Principal 意指**任何可以向系統發出請求，並被系統驗證身份的實體**。

## User Principal

代表一個真實的自然人。通過 Subject、UserID、Email 等方式標記，權限是動態的，根據登入後的 Session 決定。

## Service Principal

非自然人的實體，例如後端服務或是工作排程，通過 `client_id` 或是 `app_id` 進行標記。使用長期憑據或是臨時憑證，且授權範圍需要嚴格管控。

## Group Principal

用來集合多個 User 或 Service Principal。作為權限分配的仲介，只需要將其他 Principal 加入或移除群組，而不必去調整每個資源的 ACL。這大大降低了管理複雜度。

## Role Principal

臨時性的身分，實作上可以通過 **Assume** 來取得權限。這可以讓 Principal 本身不帶權限，只有在特定情境下獲得其角色擁有的權限。

---

在請求流程中，通過以下操作：

1. 驗證身分
2. 建立 Pricipal
3. 授權決策
4. 審計紀錄

一旦 Principal 建立，後續流程不該再關心登入的來源。

| 要素     | 說明                                                    | 範例                                             |
| -------- | ------------------------------------------------------- | ------------------------------------------------ |
| 唯一性   | 不可變的唯一標識符，絕對不能用會變動的資料(Ex. Email)。 | 550e8400-e29b-41d4-a716-446655440000             |
| 可認證   | 用來證明身份的東西。                                    | 密碼雜湊、公鑰 (RSA/ED25519)、API Key。          |
| 聲明     | 關於Principal的附加資訊，用於授權判斷。                 | department: finance, clearance_level: top_secret |
| 租戶標識 | 在多租戶架構中，區分Principal屬於哪個客戶。             | org_id: 9982                                     |

