---
title: REST API
---

REST API (具象狀態傳輸應用程式設計介面) 是一種架構樣式，通常視為設計及建構網路應用程式的標準，而這些應用程式正是網路的基礎。只要遵守這套架構底下的規則和限制，就可以實作一個簡單且易於擴充的 Web 服務。

## 核心概念

它的核心概念是：透過 HTTP 操作資源（resource）。系統將資料或物件表示為資源，每個資源都有唯一的 URI，客戶端透過 HTTP 方法對這些資源進行操作。

![REST API](https://www.gstatic.com/bricks/image/321862a0-3a14-4abb-a148-36bb8781c0f7.png)

- 標準 HTTP 動詞來表示動作：GET 為擷取，POST 為建立，PUT 為更新，DELETE 則為移除。這樣就能建立可預測且一致的介面。
- URI 代表資源，因此應使用名詞 (如為集合則使用複數，如為特定項目則使用單數或 ID)，而非動詞。例如，應以 `/users` 代表所有使用者，而非 `/getAllUsers`。

## REST API 的優點

- 簡單可讀
  > 採用標準 HTTP 方法和人類可讀的 URI，因此開發人員相當容易學習、使用這類介面並偵錯。此外，REST API 屬於自我描述性介面，可簡化整合作業。
- 擴充性
  > 無狀態架構這點是 REST 具備高擴充性的一大關鍵。由於伺服器不必保留用戶端工作階段，系統很容易就能將要求分散到多個伺服器，還能加入新伺服器來處理增加的負載，完全不需要複雜程序。
- 用戶端與伺服器分離
  > 落實用戶端-伺服器架構，分離關注點，讓開發人員能分別處理用戶端前端和伺服器端後端作業，進而加快開發週期。
- 跨語言
  > 標準 HTTP 為基礎的架構類型，因此能以各種程式設計語言實作，且適用於任何可發出 HTTP 要求的用戶端，在不同技術堆疊之間提供最大互通性。

以交通部的 [MOTC](https://tdx.transportdata.tw/api-service/swagger#/CityBus) 為例子：

```bash
curl -X 'GET' \
  'https://tdx.transportdata.tw/api/basic/v2/Bus/RealTimeByFrequency/Streaming/City/Hsinchu?%24top=30&%24format=JSON' \
  -H 'accept: application/json'
```

收到回應

```json
[
  {
    "PlateNumb": "288-U7",
    "OperatorID": "13",
    "OperatorNo": "1303",
    "RouteUID": "HSZ0160",
    "RouteID": "0160",
    "RouteName": {
      "Zh_tw": "16",
      "En": "16"
    },
    "SubRouteUID": "HSZ016002",
    "SubRouteID": "016002",
    "SubRouteName": {
      "Zh_tw": "16",
      "En": "16"
    },
    "Direction": 1,
    "BusPosition": {
      "PositionLon": 120.971921666667,
      "PositionLat": 24.8173633333333,
      "GeoHash": "wsqj0vc5d"
    },
    "Speed": 22,
    "Azimuth": 222,
    "DutyStatus": 0,
    "BusStatus": 0,
    "MessageType": 1,
    "GPSTime": "2026-03-18T11:24:03+08:00",
    "SrcRecTime": "2026-03-18T11:24:03+08:00",
    "SrcTransTime": "2026-03-18T11:24:03+08:00",
    "UpdateTime": "2026-03-18T11:24:04+08:00"
  },
  ...
]
```

## 設計 REST API 的概念

### 針對資源名稱使用名詞

使用名詞來表示資源。 例如，使用 `/contest` 而不是 `/create-contest`。 HTTP GET、POST、PUT、PATCH 和 DELETE 方法已經表示動作意義。

### 使用複數名詞來命名集合 URI

一般而言，它有助於針對參考集合的 URI 使用複數名詞。最好將集合和專案的 URI 組織成階層。

- `/contests`：表示一組競賽的題目
- `/contests/{problemId}`：表示一組競賽的某個題目內容
- `/contests/{problemId}/{submitId}`：表示一組競賽的某個題目的提交狀況

這種方法有助於讓 REST API 保持直覺。

### 考慮不同資源類型之間的關聯

比方說，你可以使用 `users/{userId}/submiited` 表示一個使用者的所有 Online Judge 提交紀錄。

也可以藉由從**Problem**到**User**的角度來呈現關聯性，以不同方向來處理關係。在這裡案例中，URI 可能是 `/problems/{problemId}/passed`。 不過，擴充此模型太遠可能會變得繁瑣而無法實作。

另外一種作法是在 HTTP 回應消息的本文中包含連結，讓用戶端可以輕鬆地存取相關資源。

```json
{
  "userId": "b9dc3d6d",
  "email": "yhchen.space@gmail.com",
  "displayName": "Yan Hao, Chen",
  "links":[
    {
      "rel":"problem",
      "href":"https://myapi.org/users/b9dc3d6d/resolved",
      "action":"GET",
      "types":["application/json"]
    },
    {
      "rel":"submmition",
      "href":"https://myapi.org/users/b9dc3d6d/submmition",
      "action":"GET",
      "types":["application/json"]
    },
    ...
  ]
}
```

在此範例中的 `links` 陣列有一組連結。每個連結都代表一個實體。 每個鏈接的數據都包含關聯性、URI、HTTP 方法，以及支援的MIME類型。

這種作法稱為**HATEOAS**(超媒體即應用狀態引擎)。客戶端與網絡應用程式交互，其應用程式伺服器通過超媒體（Hypermedia）動態提供信息。除了對超媒體的一般理解之外，REST客戶端幾乎不需要關於如何與應用程式或伺服器交互的先驗知識。

> 這種和其他 REST 約束的一些嚴格性的目的是「數十年規模的軟體設計：每個細節都旨在促進軟體壽命和獨立進化。許多約束直接與短期效率相關。不幸的是，人們在短期設計方面相當擅長，而在長期設計方面通常很糟糕」。
>  *Fielding, Roy T.*
