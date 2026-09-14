# 台灣圖霸 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸 | Map8 Platform** 地圖平台

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#api-distance-matrix-api

## Version
- v3.1_2025-09-19 (the present document)

## [Routes] 路徑規劃
功能 : 多點路徑規劃、多點旅行之距離時間矩陣

## API Index
- [Directions API (多點路徑規劃)](./directions-api.md)
- [Distance Matrix API (排班 : 多點運算交通時間距離矩陣)](#distance-matrix-api)
- [Trip API (排行程 : 多途經點排序路徑規劃)](./trip-api.md)

### Distance Matrix API
(排班) 運算多對多起訖點之交通時間距離矩陣 (Distance Matrix)。

請注意，本 API 提供您兩個方式來指定出發地與目的地 :

1. (方式一) `<地點座標組>` : 先列出所有地點再指明哪些各為出發地與目的地
    - 此方式乃先以 GPS 座標方式列舉地點。
    - 可再搭配以 `sourceIndices` 與 `destinationIndices` (兩者均為索引值陣列) 來指定 <地點座標組> 其中的哪些地點分別各為出發地、目的地。
    - 若 `sourceIndices` 與 `destinationIndices` 未給，則 `<地點座標組>` 內的全部座標將均既作為出發地，也為目的地，來運算出矩陣。
2. (方式二) `origins / destinations` : 出發地與目的地各自獨立列舉
    - 此方式為不先將座標全數列出 (再指明出發地 / 目的地)。
    - 反之，而是出發地與目的地，各自獨立列舉 (如同 Google Maps Platform 的 Distance Matrix API)。
    - 此外，出發地與目的地的列舉方式 (均為陣列形式)，除了座標，還可改以直接給地址。或是 [Places API](../places/places-api.md) 所回應出的 `place_id`。

> 請注意 : 以上兩種方式為二擇一。

> 請留意 : 本 API 支援的出發地與目的地數量，最多各為 100 個。而出發地的數量，乘上目的地的數量，最多則為 100 個。

> 請留意 : URL 必須正確 [編碼](https://en.wikipedia.org/wiki/Percent-encoding)，並且所有 Map8 API 均限制在最多 8192 個字元。當您建立呼叫 Map8 API 的 URL 時，請務必留意到此限制。

- **API** :

    ```
    https://api.map8.zone/distancematrix/<交通工具>/<地點座標組>.json?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **交通工具**
        - 可為 `car` (汽車)、`bicycle` (自行車)、或 `foot` (步行)。
    - **地點座標組**
        - 選擇性參數 : 此即上述 (方式一) 之以 GPS 座標方式指定地點。後續可搭配以 `sourceIndices` 與 `destinationIndices` 來指定 <地點座標組> 其中的哪些地點分別作為運算矩陣內所對應的出發地與目的地。
        - 格式為 : `<地點之經度>,<地點之緯度>;<地點之經度>,<地點之緯度>;...;<地點之經度>,<地點之緯度>.json`。亦即，乃以逗號分隔之 `<經度>,<緯度>` 座標為一組，然後以分號分隔連接數組座標 (請注意，座標格式是 `<經度>,<緯度>`，而非 `<緯度>,<經度>`)。
        - 請注意 : `(方式一) <地點座標組>` 與 `(方式二) origins / destinations` 為二擇一。若採方式二，URL 路徑為 `https://api.map8.zone/distancematrix/<交通工具>/json`。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **sourceIndices**
            - 選擇性參數 : 若採取以 `(方式一) <地點座標組>` 方式指定地點，則應以此參數來指定出發地。
            - 格式為 : `<index>, ..., <index>` (逗號分隔) -- i.e., 選擇出 `地點座標組` 內的何者作為運算矩陣的出發地 (索引值以零起算)。
            - 當採取 `地點座標組` 的方式來指定地點，若此參數未給，則預設將 `地點座標組` 內的全部座標均作為出發地。
        - **destinationIndices**
            - 選擇性參數。格式同上述 `sourceIndices`。用以指定運算矩陣之目的地。(同上述 `sourceIndices`，若此參數未給，則預設將 `地點座標組` 內的全部座標均作為目的地。)
        - **origins**
            - 選擇性參數 : 此即上述 `(方式二) origins / destinations` : 讓您可以改以更為直覺的方式來指定數個出發地。透過本參數，您可以採用 `place_id` 方式 (參見 [Find Place API](../places/places-api.md#find-place-api)) 來指定出發地，甚或直接給予地址，讓台灣圖霸的 [Geocoding API](../places/geocoding-api.md) 自動為您進行搜尋定址。當然，您也可以在這個參數內直接給入座標。
            - 格式為 : `<地址>|...|<地址>` -- i.e., 以豎線 (pipe character `|`) 分隔之數個地點。其中，每個 `<地址>` 有底下三種選擇 :
                1. 採用台灣圖霸 Places API 所回覆給您的 `place_id` (例如 `place_id:NzYqAQYCRh4EW19THlt1Uys9NR5eQQNTQg8EWHRZEUlfNQFyWURBEg==`)。
                2. 地址 (例如 : `台北市內湖區港墘路200號4樓之3`)。
                3. 座標 (格式是 `<經度>,<緯度>`)。
        - **destinations**
            - 選擇性參數。格式同上述 `origins`。用以指定運算矩陣之目的地。
        - **annotations**
            - 選擇性參數。可為 `duration` 或 `distance` 或 `duration,distance` (逗號分隔)。
        - **language**
            - 選擇性參數。可為 `en_US` 或 `zh_TW`。預設為 `zh_TW`。用來顯示矩陣運算結果內的 `duration` / `distance` 之 `text` 欄位 (將時間的秒、與距離的公尺數值，直接替您解譯成人類可以直接閱讀的字串。譬如 4096 會為您轉譯為 4.1 公里，而 7165 秒會為您轉譯為 1 hour 59 mins)。
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
              "destinations" : [            // 目的地之座標資訊
                "routePoint": {             // 實際用以計算路徑規劃的精確座標 (i.e., 輸入的座標經過黏到道路後的結果)
                  "distance" : <Number>,    // 實際用以計算路徑規劃的精確座標與所給之地點的距離 (單位為公尺)
                  "location" : [            // 實際用以計算路徑規劃的精確座標。為一帶有兩個元素之陣列，為 [<經度>, <緯度>]
                    <Number>, <Number>
                  ]
                  "name" : <String>,        // 實際用以計算路徑規劃的精確座標所在的道路名稱
                },
                "place": {
                  ...                       // 此即為 Geocoding API 的 [地址定位] 的回應結果 results[] 陣列中的物件。請詳見 Geocoding API 的 [Response > 地址定位] 章節的內容
                },
                ... (more results)...
              ],
              "origins" : [             // 出發地之座標資訊
                ...                     // 結構同上 `destinations`
              ],
              "rows": [                 // 此為運算結果的矩陣。`rows` 的每一列，表示每個出發地。其中，`elements` 陣列，即為每個出發地到每個目的地的計算結果
                {
                  "elements": [         // 某出發地對每個目的地之運算結果陣列
                    {
                      "duration": {
                        "value": <Number>   // 計算得的數值
                        "text": <String>    // 上開數值，依據語系轉譯成文字。譬如，374 將轉譯為 "6 分鐘" (若 `language` 參數為 "zh_TW")
                      },
                      "distance": {
                        "value": <Number>   // 計算得的數值
                        "text": <String>    // 上開數值，依據語系轉譯成文字。譬如，1900 將轉譯為 "1.9 公里" (若 `language` 參數為 "zh_TW")
                      },
                      "status": "OK"        // 處理完成
                    },
                    ... (more results) ...
                  ]
                }
              ],
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

- **Example (1)** : 此例以 `car` (汽車) 作為交通工具，並採取 `(方式一) <地點座標組>` 方式。透過 `sourceIndices` 與 `destinationIndices` 均不指定的方式，直接將全部的地點均作為出發地與目的地運算出矩陣。

    ```
    HTTP GET "https://api.map8.zone/distancematrix/car/121.579839,25.065064;121.576499,25.068178;121.579343,25.068131.json?key=<您的 key>"
    ```

- **Example (2)** : 此例以 `car` (汽車) 作為交通工具，採取 `(方式一) <地點座標組>` 方式，並透過 `sourceIndices` 與 `destinationIndices` 指定出發地與目的地來運算出矩陣。

    ```
    HTTP GET "https://api.map8.zone/distancematrix/car/121.579839,25.065064;121.576499,25.068178;121.579343,25.068131.json?key=<您的 key>&sourceIndices=0,1&destinationIndices=1,2"
    ```

- **Example (3)** : 此例以 `car` (汽車) 作為交通工具，並採取 `(方式二) origins` / `destinations` 方式指定出發地與目的地。

    ```
    HTTP GET "https://api.map8.zone/distancematrix/car/json?key=<您的 key>&origins=place_id:NzYqAQYCRh4EW19THlt1Uys9NR5eQQNTQg8EWHRZEUlfNQFyWURBEg==&destinations=圖霸科技|內湖區行政中心&language=zh_TW"
    ```
    上開範例 (3) 的回應結果 :
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "destinations": [
            {
                "routePoint": { "location": [121.574564, 25.075867], "name": "港墘路", "distance": 8 },
                "place": {
                    "formatted_address": "台北市內湖區港墘路200號4樓之3",
                    "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
                    "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
                    "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
                    "name": "圖霸科技股份有限公司",
                    "city": "台北市",
                    "town": "內湖區",
                    "type": "地點",
                    "level": "fuzzy",
                    "likelihood": 75.59,
                    "authoritative": "false"
                }
            },
            {
                "routePoint": { "location": [120.343759, 22.629392], "name": "光復路二段", "distance": 8 },
                "place": {
                    "formatted_address": "高雄市鳳山區光復路二段132號(高雄市政府鳳山行政中心內)",
                    "geometry": { "location": { "lat": 22.629464, "lng": 120.343762 } },
                    "id": "NzYqAQYDQBgGXl1TGDxUCkwtMgV/cFNqFEVQfxdCCzcrMEFnfkdFEg==",
                    "place_id": "NzYqAQYDQBgGXl1TGDxUCkwtMgV/cFNqFEVQfxdCCzcrMEFnfkdFEg==",
                    "name": "中華郵政鳳山行政中心郵局",
                    "city": "高雄市",
                    "town": "鳳山區",
                    "type": "地點",
                    "level": "fuzzy",
                    "likelihood": 46.15,
                    "authoritative": "false"
                }
            }
        ],
        "origins": [
            {
                "routePoint": { "location": [121.574564, 25.075867], "name": "港墘路", "distance": 8 },
                "place": {
                    "formatted_address": "台北市內湖區港墘路200號4樓",
                    "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
                    "id": "NzYqAQYCRh4EW19THlt1Uys9NR5eQQNTQg8EWHRZEUlfNQFyWURBEg==",
                    "place_id": "NzYqAQYCRh4EW19THlt1Uys9NR5eQQNTQg8EWHRZEUlfNQFyWURBEg==",
                    "name": "研勤科技股份有限公司(PAPAGO)",
                    "city": "台北市",
                    "town": "內湖區",
                    "type": "地點",
                    "level": "1",
                    "likelihood": -1,
                    "authoritative": "false"
                }
            }
        ],
        "rows": [
            {
                "elements": [
                    { "duration": { "value": 1, "text": "1 分鐘" }, "distance": { "value": 0, "text": "1 公尺" }, "status": "OK" },
                    { "duration": { "value": 30208, "text": "8 小時 23 分鐘" }, "distance": { "value": 352860, "text": "353 公里" }, "status": "OK" }
                ]
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
