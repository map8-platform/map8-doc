# 台灣圖霸 | Map8 Platform
# 附錄 (Appendix)
歡迎使用 **<img src="../images/logo.png" width="28" height="28"> 台灣圖霸 | Map8 Platform** 地圖平台

- [HTTP Status Code](#http-status-code)
- ["status" 欄位](#status-欄位)
- [地點類型](#地點類型)

## HTTP Status Code
以上 API，可能回傳的 HTTP status code 如后 :

| Error Code | 意義 |
|---|---|
| 400 | Bad Request -- 表示您的 request 解析有誤。通常是給入的參數多了或少了，或是格式有錯誤，或必要參數卻沒給，等等 |
| 401 | Unauthorized -- 表示您未給定您的 key，或是您給的 key 並非有效。請跟我們聯絡 |
| 503 | Service Unavailable -- 表示您的 request 已經超出與我們約定的 QoS (服務品質) 等級。通常過一會兒 (QoS 上限解除) 再重發一次即可成功。如果持續發生，請跟我們聯絡 |

## "status" 欄位
以上 API，可能回傳之 `status` 欄位的意義為 :

| `status` 值 | 意義 |
|---|---|
| `OK` | 無發生任何錯誤；該地點被成功偵測，並且至少回傳一則結果 |
| `ZERO_RESULTS` | 表示搜尋雖然完成，但未得到任何有效結果。此狀況譬如可能發生在您對本 API 發出的 request 所給定的中心座標在一個偏遠地區 |
| `OVER_QUERY_LIMIT` | 表示您已經超出您的配額。請跟我們聯絡 |
| `REQUEST_DENIED` | 表示您的 request 無法進行；一般來說是您未給定您的 key，或是您給的 key 並非有效。請跟我們聯絡 |
| `INVALID_REQUEST` | 表示您的 request 解析有誤。通常是給入的參數多了或少了，或是格式有錯誤，或必要參數卻沒給，等等 |
| `UNKNOWN_ERROR` | 表示是我們的伺服器端的錯誤；再重試一次可能就會成功。如果持續發生此問題，請跟我們聯絡 |

> **(Migration 指南) 與 Google Maps 的 Find Place API 相容性** : 以上 "status" 欄位與 Google Maps 完全相容

## 地點類型
所支援的景點類型 (`cat` 參數之值)，為字串，如下 :

公路里程、交流道入口、國道設施、國小、郵局、農漁會、休閒渡假、遊樂園區、寺廟、風景區、農牧事業區、地名、幼兒園、圖書館、村里、消防單位、軍警單位、山岳、國中、電信公司、加油站、公眾服務、社福機構、藝文美術、停車場、文化紀念、公司行號、民宿、古蹟、政府機關、衛生所、便利商店、購物商場、旅遊服務中心、溫泉、墓園、飯店旅館、森林遊樂區、高中職校、中式美食、露營營地、湖泊水庫、咖啡茶藝、博物館、公園、體育館、診所、糕點烘焙、汽車展售、藥局、輪車保修、貨運站、異國美食、生活量販、公車站 (*註)、銀行、3C賣場、居家修繕、不動產、飲料冰品、超級市場、影視娛樂、補習進修、傳統市場、醫院、高爾夫、汽車百貨、書局、工業區、行人出入口、體育場、證券投資、保險公司、速食、火車站、寵物、夜市、地方小吃、觀光工廠、大專院校、污水處理場、國家公園、其他、渡口碼頭、纜車站、商圈、信用合作社、租車公司、教堂、素食、百貨公司、外語學校、拖吊場、動物園、自行車道、夜生活、漁港、海濱遊憩、美容美髮、機場、出入口、高鐵站、捷運站、大樓、汽車出入口、商港、自行車出入口、社區、加氣站、充電站、公益彩券、ATM、快捷巴士

> *註 : `公車站` 類別請參考尊爵版 API : [Amenity API](https://api.map8.zone/docs/premium-housing-api-docs/map8-premium-housing-api.html?key=%3C%E6%82%A8%E7%9A%84Key%3E)

----

<p align="center">
<a href="https://map8.zone"><img src="../images/logo_96x96.png" /></a> <br/> https://map8.zone
</p>
