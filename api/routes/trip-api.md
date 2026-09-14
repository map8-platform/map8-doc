# 台灣圖霸 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸 | Map8 Platform** 地圖平台

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#api-trip-api

## Version
- v3.1_2025-09-19 (the present document)

## [Routes] 路徑規劃
功能 : 多點路徑規劃、多點旅行之距離時間矩陣

## API Index
- [Directions API (多點路徑規劃)](./directions-api.md)
- [Distance Matrix API (排班 : 多點運算交通時間距離矩陣)](./distance-matrix-api.md)
- [Trip API (排行程 : 多途經點排序路徑規劃)](#trip-api)

### Trip API
(排行程) 給定多途經點，排出次序並規劃路徑 (旅行業務員 / Travelling Salesman Problem (TSP) 最短路徑問題)

> 由於 Travelling Salesman Problem 為 NP-hard 問題，因此，系統無法保證於要求回應時間內所能求出之順序為最佳解。若給定的地點少於十個，系統將以暴力法求出最佳解。但若超過，則以 greedy heuristic 手法 (Farthest-Insertion 演算法) 求出近似解回傳。

請注意，本 API 提供您兩個方式來指定地點 :

1. (方式一) `<地點座標組>` : 以 GPS 座標方式列舉地點。
2. (方式二) `<地點陣列> 給入地址或地名` :
    - 透過 `waypoints` 參數，直接給予地址 (或地點名稱)，讓台灣圖霸的 [Geocoding API](../places/geocoding-api.md) 自動為您進行搜尋定址。
    - 或是 `place_id` 方式 (參見 [Find Place API](../places/places-api.md#find-place-api)) 來指定地點。
    - 當然，您也可以在這個參數內直接給入座標。

> 請注意 : 以上兩種方式為二擇一。

> 請留意 : 本 API 支援的地點總數最多為 300 個。

> 請留意 : URL 必須正確 [編碼](https://en.wikipedia.org/wiki/Percent-encoding)，並且所有 Map8 API 均限制在最多 8192 個字元。當您建立呼叫 Map8 API 的 URL 時，請務必留意到此限制。

- **API** :

    ```
    https://api.map8.zone/trip/<交通工具>/<地點座標組>.json?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **交通工具**
        - 可為 `car` (汽車)、`bicycle` (自行車)、或 `foot` (步行)。
    - **地點座標組**
        - 選擇性參數 : 此方式乃以 GPS 座標方式指定地點。
        - 格式為 : `<地點之經度>,<地點之緯度>;<地點之經度>,<地點之緯度>;...;<地點之經度>,<地點之緯度>.json`。亦即，乃以逗號分隔之 `<經度>,<緯度>` 座標為一組，然後以分號分隔連接數組座標 (請注意，座標格式是 `<經度>,<緯度>`，而非 `<緯度>,<經度>`)。
        - 若採方式二 (`waypoints`)，URL 路徑為 `https://api.map8.zone/trip/<交通工具>/json`。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **waypoints**
            - 選擇性參數 : 除了以 `地點座標組` 的方式來指定，您可以改採更為直覺的方式來指定數個地點。透過本參數，您可以採用 `place_id` 方式來指定地點，甚或直接給予地址，讓台灣圖霸的 Geocoding API 自動為您進行搜尋定址。當然，您也可以在這個參數內直接給入座標。
            - 格式為 : `<地點>|...|<地點>` -- i.e., 以豎線 (pipe character `|`) 分隔之數個地點。其中，每個 `<地點>` 有底下三種選擇 :
                1. 採用台灣圖霸 Places API 所回覆給您的 `place_id` (例如 `place_id:NzYqAQYCRh4EW19THlt1Uys9NR5eQQNTQg8EWHRZEUlfNQFyWURBEg==`)。
                2. 地址 (例如 : `台北市內湖區港墘路200號4樓之3`)。
                3. 座標 (格式是 `<經度>,<緯度>`)。
        - **source**
            - 選擇性參數 : 可為 `any` 或 `first`。後者 (`first`) 表示欲將透過 `地點座標組` 或 `waypoints` 所指定之地點中的第一個地點作為求最佳路徑順序的起點。`any` 表示不限制 (所給之任一地點均可作為起點)。預設為 `any`。請注意系統進行路徑規劃之運算對於 `source`, `destination` 與 `roundtrip` 有互相搭配的要求。詳見底下表格。
        - **destination**
            - 選擇性參數 : 可為 `any` 或 `last`。後者 (`last`) 表示欲將透過 `地點座標組` 或 `waypoints` 所指定之地點中的最後一個地點作為求最佳路徑順序的終點。`any` 表示不限制 (所給之任一地點均可作為終點)。預設為 `any`。
        - **roundtrip**
            - 選擇性參數 : 指定所求最佳路徑順序是否要求返回起點 (`true` / `false`; 預設為 `true`; 請注意 `true` / `false` 值以如字面 (string literal) 帶入，而非以 1 / 0 或其它字元帶入)。
            - `source`, `destination` 與 `roundtrip` 必須互相搭配。系統目前支援的組合如下表 :

                | source | destination | roundtrip | 支援與否 |
                |---|---|---|---|
                | `first` | `last` | `true` | 是 |
                | `first` | `any` | `true` | 是 |
                | `any` | `last` | `true` | 是 |
                | `any` | `any` | `true` | 是 |
                | `first` | `last` | `false` | 是 |
                | `first` | `any` | `false` | 否 |
                | `any` | `last` | `false` | 否 |
                | `any` | `any` | `false` | 否 |

            - 若您指定的組合不受支援，則系統會回應您 `{"status":"INVALID_REQUEST","message":"NotImplemented"}`。
        - **language**
            - 選擇性參數。可為 `en_US` 或 `zh_TW`。預設為 `zh_TW`。用來顯示運算結果內的 `duration` / `distance` 之 `text` 欄位 (直接將時間 / 距離的秒 / 公尺數值，為您解析成人類可以直接閱讀的字串。譬如 4096 會為您轉譯為 4.1 公里，而 7165 秒會為您轉譯為 1 hour 59 mins)。
        - **overview**
            - 選擇性參數 : 是否需要傳回所規劃路徑的路線總覽 (可供您用於將路線繪製在 Map8 地圖上。請取用 `geometry` 欄位)。可為 :
                - `simplified` : 傳回精簡資料。
                - `false` : 不傳回。
                - `full` (預設值) : 傳回最詳細資料，以繪製出圖面上最為美觀的路徑幾何呈現，不會因為距離長短而有所解析度損失。
        - **geometries**
            - 選擇性參數，可為 `polyline`, `polyline6`, 或 `geojson` (預設為 `geojson`)。
- **Request Message Body** : None.
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
        - **Response Message Body** :
            - Content-type: application/json
            - 回傳的結構的各個欄位的資訊所代表之意義如后

            ```
            {
              "status" : "Ok",              // 處理完成
              "trips" : [
                {
                  "waypoints" : [                 // 地點資訊陣列 (維持原始輸入順序，實際行經順序需依 waypoint_index 重新排序)
                    {
                      "routePoint": {             // 實際用以計算路徑規劃的精確座標 (i.e., 輸入的座標經過黏到道路後的結果)
                        "distance" : <Number>,    // 實際用以計算路徑規劃的精確座標與所給之地點的距離 (單位為公尺)
                        "location" : [            // 實際用以計算路徑規劃的精確座標。為一帶有兩個元素之陣列，為 [<經度>, <緯度>]
                          <Number>, <Number>
                        ]
                        "name" : <String>,        // 實際用以計算路徑規劃的精確座標所在的道路名稱
                        "waypoint_index": <Number>,   // 此地點於系統計算之最佳行經順序中的順位 (從 0 起算) 內之索引編號
                      },
                      "place": {
                        ...                       // 此即為 Geocoding API 的 [地址定位] 的回應結果 results[] 陣列中的物件。請詳見 Geocoding API 的 [Response > 地址定位] 章節的內容
                      },
                    },
                    ... (more results)...
                  ],
                  "duration": {
                    "value": <Number>   // 計算得的數值
                    "text": <String>    // 上開數值，依據語系轉譯成文字。譬如，374 將轉譯為 "6 分鐘" (若 `language` 參數為 "zh_TW")
                  },
                  "distance": {
                    "value": <Number>   // 計算得的數值
                    "text": <String>    // 上開數值，依據語系轉譯成文字。譬如，1900 將轉譯為 "1.9 公里" (若 `language` 參數為 "zh_TW")
                  },
                  "geometry" : {
                    "coordinates" : [Array],  // 為一陣列，每個元素帶有兩個元素之陣列，分別為經度與緯度 (i.e., 此 `geometry` 陣列的每個元素為 [<經度>,<緯度>])。用以於地圖之圖面上繪製完整、美觀的路線圖
                    "type": "LineString"
                  },
                },
                ... (more results)...
              ]
            }
            ```
    - Status code : **400** Bad Request
        - 表示您的 request 系統偵測到有錯誤而無法完成您的要求。通常是給入的參數多了或少了，或是格式有錯誤，或必要參數卻沒給，等等
        - **Response Message Body** :
            - Content-type: application/json

            ```
            {
              "status" : <String>     // Status Code
            }
            ```
    - 參見 [HTTP Status Code](../appendix.md#http-status-code) 一節說明本 API 回傳值之一般通則

- **Example (1)** : 此例以 `car` (汽車) 作為交通工具，並採取 `地點座標組` 方式。不指定任何地點身為起點或終點，由系統預設返回起點的方式，進行路徑規劃並求出行程上每個地點都必須通過的最佳順序路徑 (語系指定為英文)。

    ```
    HTTP GET "https://api.map8.zone/trip/car/121.574494,25.075904;121.574494,25.075904;121.585842,25.081139;121.575922,25.062935.json?key=<您的 key>&language=en_US"
    ```

- **Example (2)** : 此例以 `car` (汽車) 作為交通工具，採取 `地點陣列` 方式，指定起點並返回為條件，進行路徑規劃並求出行程上每個地點都必須通過的最佳順序路徑。

    ```
    HTTP GET "https://api.map8.zone/trip/car/json?key=<您的 key>&waypoints=圖霸科技|碧湖公園|美麗華百樂園&source=first&roundtrip=true"
    ```
    上開範例 (2) 的回應結果，您可以見到，給定的地點依序為 `圖霸科技 (編號 0)`、`碧湖公園 (編號 1)`、`美麗華百樂園 (編號 2)`。由於 `waypoints` 陣列仍維持原始輸入順序，實際最佳行經順序需依各 `waypoint` 之 `waypoint_index` 由小至大重新排序 (0 → 1 → 2) 後解讀，可得系統規劃出之最佳順序為 `圖霸科技` ⇒ `美麗華百樂園` ⇒ `碧湖公園` ⇒ `圖霸科技` (終點是因為指定了 `roundtrip=true` 要求返回起點)。如下 :
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "trips": [
            {
                "waypoints": [
                    {
                        "routePoint": { "waypoint_index": 0, "location": [121.574564, 25.075867], "name": "港墘路", "distance": 8 },
                        "place": {
                            "formatted_address": "台北市內湖區港墘路200號4樓之3",
                            "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
                            "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
                            "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
                            "name": "圖霸科技股份有限公司",
                            "city": "台北市", "town": "內湖區", "type": "地點",
                            "level": "fuzzy", "likelihood": 75.59, "authoritative": "false"
                        }
                    },
                    {
                        "routePoint": { "waypoint_index": 2, "location": [121.585888, 25.08098], "name": "內湖路二段", "distance": 18 },
                        "place": {
                            "formatted_address": "台北市內湖區",
                            "geometry": { "location": { "lat": 25.081139, "lng": 121.585842 } },
                            "id": "NzYqAQYCRh4BWFlTGFtaLUxPSwBXUSJUNF94Glt7SDYTSC1DRXE8Eg==",
                            "place_id": "NzYqAQYCRh4BWFlTGFtaLUxPSwBXUSJUNF94Glt7SDYTSC1DRXE8Eg==",
                            "name": "碧湖公園",
                            "city": "台北市", "town": "內湖區", "type": "地點",
                            "level": "fuzzy", "likelihood": 100, "authoritative": "false"
                        }
                    },
                    {
                        "routePoint": { "waypoint_index": 1, "location": [121.556378, 25.083069], "name": "敬業三路", "distance": 30 },
                        "place": {
                            "formatted_address": "台北市中山區敬業三路20號",
                            "geometry": { "location": { "lat": 25.083068, "lng": 121.556674 } },
                            "id": "NzYqAQYCRxsKV15TJiZiMBUIGht8RA9KIFtCSXN3BBMMMzZeUHZFEg==",
                            "place_id": "NzYqAQYCRxsKV15TJiZiMBUIGht8RA9KIFtCSXN3BBMMMzZeUHZFEg==",
                            "name": "美麗華百樂園",
                            "city": "台北市", "town": "中山區", "type": "地點",
                            "level": "fuzzy", "likelihood": 81.65, "authoritative": "false"
                        }
                    }
                ],
                "duration": { "value": 1281, "text": "21 分鐘" },
                "distance": { "value": 7092, "text": "7.1 公里" },
                "geometry": {
                    "coordinates": [
                        [121.574564, 25.075867],
                        [121.574525, 25.075806],
                        ... (此為經緯度座標陣列, 供繪製地圖之用; 略)...
                    ],
                    "type": "LineString"
                }
            }
        ],
        "status": "OK"
    }
    ```

- [back to index](#api-index)

----

<p align="center">
<a href="https://map8.zone"><img src="../../images/logo_96x96.png" /></a> <br/> https://map8.zone
</p>
