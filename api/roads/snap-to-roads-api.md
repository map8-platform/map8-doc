# 台灣圖霸 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸 | Map8 Platform** 地圖平台

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#api-snap-to-roads-api

## Version
- v3.1_2025-09-19 (the present document)

## [Roads] 道路資訊
功能 : 取得道路屬性與黏路

## API Index
- [Nearest Roads API (道路屬性)](./nearest-roads-api.md)
- [Snap to Roads API (黏路)](#snap-to-roads-api)

### Snap to Roads API
給定一路徑依序之座標點，對地圖黏岀最可能路徑 (黏路)。

> 請留意 : 本 API 支援的 <路徑點座標組> 總數最多為 100 個。

> 請留意 : URL 必須正確 [編碼](https://en.wikipedia.org/wiki/Percent-encoding)，並且所有 Map8 API 均限制在最多 8192 個字元。當您建立呼叫 Map8 API 的 URL 時，請務必留意到此限制。

- **API** :

    ```
    https://api.map8.zone/road/snapToRoads/<交通工具>/<路徑點座標組>.json?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **交通工具**
        - 可為 `car` (汽車)、`bicycle` (自行車)、或 `foot` (步行)。
    - **路徑點座標組**
        - 格式為 : `<路徑起點之經度>,<路徑起點之緯度>;<路徑中途點之經度>,<路徑中途點之緯度>;...;<路徑終點之經度>,<路徑終點之緯度>.json`。亦即，乃以逗號分隔之 `<經度>,<緯度>` 座標為一組，然後以分號分隔連接數組座標 (請注意，座標格式是 `<經度>,<緯度>`，而非 `<緯度>,<經度>`)。第一組為起點，最後一組為終點。而中間的數組則為路徑上依序經過的中途點 (waypoints)。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
- **Request Message Body** : None.
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
        - **Response Message Body** :
            - Content-type: application/json
            - 回傳的結構的各個欄位的資訊所代表之意義如后

            ```
            {
              "html_attribution": [
                "台灣圖霸",
                "研鼎智能",
                "PAPAGO!"
              ],
              "status" : "Ok",                  // 處理完成
              "matchings": [                    // 黏路結果陣列; 視狀況，可能有多種黏路結果
                {
                  "tracepoints": [              // 其一黏路結果。陣列中的每個元素，依序為黏路結果的順序。每個元素並針對輸入的 `路徑點座標組` 回傳結果出來。`請注意` : 此陣列內之元素之值可能為 `null` : 表示所對應的 `路徑點座標組` 之黏路結果為失敗。
                    {
                      "alternatives_count": <Number>,   // 此黏到路的地點，另有多少個可黏到路的地點之數量 (表示有其他可能性)
                      "waypoint_index": <Number>,       // 此黏到路的地點，為整個陣列中，屬有效黏路 (i.e., 非 null) 元素中的第幾個 (索引編號; 從零起算)
                      "location": [               // 為一帶有兩個元素之陣列，為 [<經度>, <緯度>]
                        <Number>, <Number>
                      ],
                      "name": <String>,           // 此黏到路的地點被匹配到的道路名稱
                      "city": <String>,           // 此黏到路的地點被匹配到的道路之所屬城市 (例如 "台北市")
                      "town" : <String>,          // 此黏到路的地點被匹配到的道路之所屬行政區 (例如 "內湖區")
                      "distance": <Number>        // 此黏到路的地點，至被匹配到的道路的直線距離 (單位為公尺)
                    },
                    ... (more results) ...
                  ],
                  "geometry" : {
                    "coordinates" : [Array],  // 為一陣列，每個元素帶有兩個元素之陣列，分別為經度與緯度 (i.e., 此 `geometry` 陣列的每個元素為 [<經度>,<緯度>])。用以於地圖之圖面上繪製完整、美觀的路線圖
                    "type": "LineString"
                  },
                },
                ... (more results) ...
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

- **Example** : 此例以 `car` (汽車) 作為交通工具

    ```
    HTTP GET "https://api.map8.zone/road/snapToRoads/car/121.57454312639197,25.07650531765003;121.57471291748163,25.076413420485522;121.57465079878949,25.076233376861623;121.57481644863265,25.07592205080232;121.5746383750515,25.075674489756707;121.57407930683218,25.075520701582974.json?key=<您的 key>"
    ```
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "matchings": [
            {
                "tracepoints": [
                    { "alternatives_count": 0, "waypoint_index": 0, "location": [121.57458, 25.076572], "name": "瑞光路", "distance": 8 },
                    { "alternatives_count": 0, "waypoint_index": 1, "location": [121.574756, 25.076492], "name": "瑞光路", "distance": 10 },
                    { "alternatives_count": 0, "waypoint_index": 2, "location": [121.574752, 25.076184], "name": "港墘路", "distance": 12 },
                    { "alternatives_count": 0, "waypoint_index": 3, "location": [121.574732, 25.075964], "name": "港墘路", "distance": 10 },
                    { "alternatives_count": 0, "waypoint_index": 4, "location": [121.574575, 25.075709], "name": "港墘路", "distance": 7 },
                    { "alternatives_count": 0, "waypoint_index": 5, "location": [121.574155, 25.075431], "name": "港墘路", "distance": 13 }
                ],
                "geometry": {
                    "coordinates": [
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
