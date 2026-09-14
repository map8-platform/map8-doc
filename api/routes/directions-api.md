# 台灣圖霸 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸 | Map8 Platform** 地圖平台

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#api-directions-api

## Version
- v3.1_2025-09-19 (the present document)

## [Routes] 路徑規劃
功能 : 多點路徑規劃、多點旅行之距離時間矩陣

## API Index
- [Directions API (多點路徑規劃)](#directions-api)
- [Distance Matrix API (排班 : 多點運算交通時間距離矩陣)](./distance-matrix-api.md)
- [Trip API (排行程 : 多途經點排序路徑規劃)](./trip-api.md)

### Directions API
(多點) 路徑規劃功能 : 依給定之起點、中途點 (零或多個)、與目的地之順序，以 Map8 的圖資與演算法來進行多點路徑規劃。

> 請留意 : 本 API 支援的 <起訖點座標組> 總數 (包含起點、中途點、與目的地) 最多為 100 個。

- **API** :

    ```
    https://api.map8.zone/route/<交通工具>/<起訖點座標組>.json?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **交通工具**
        - 可為 `car` (汽車)、`bicycle` (自行車)、或 `foot` (步行)。
    - **起訖點座標組**
        - 格式為 : `<起點之經度>,<起點之緯度>;<中途點之經度>,<中途點之緯度>;...;<目的地之經度>,<目的地之緯度>.json`。亦即，乃以逗號分隔之 `<經度>,<緯度>` 座標為一組，然後以分號分隔連接數組座標 (請注意，座標格式是 `<經度>,<緯度>`，而非 `<緯度>,<經度>`)。第一組為起點，最後一組為目的地。而中間的數組則為要求路徑上必須經過的中途點 (waypoints)。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **alternatives**
            - 選擇性參數 : 是否多路徑規劃 (`true` / `false`; 預設為 `false`; 請注意 `true` / `false` 值以如字面 (string literal) 帶入，而非以 1 / 0 或其它字元帶入)。
        - **steps**
            - 選擇性參數 : 是否需傳回所規劃路徑上的每一個詳細轉彎資訊 (`true` / `false`; 預設為 `false`)。
            - 請注意 : 此逐轉彎的詳細資訊，視所要求路徑規劃之情況，資料量可能相當龐大。
        - **overview**
            - 選擇性參數 : 是否需要傳回所規劃路徑的路線總覽 (可供您用於將路線繪製在 Map8 地圖上。請取用 `geometry` 欄位)。可為 :
                - `simplified` : 傳回精簡資料。
                - `false` : 不傳回。
                - `full` (預設值) : 傳回最詳細資料，以繪製出圖面上最為美觀的路徑幾何呈現，不會因為距離長短而有所解析度損失。
            - (請注意以上之傳入值為如字面 (string literal) 帶入，而非以 1 / 0 或其它字元帶入)。
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
              "waypoints" : [                 // 起點、中途點、或目的地之座標資訊
                {
                  "distance" : <Number>,      // 自起點至此中途點的直線距離 (單位為公尺)
                  "location" : [              // 為一帶有兩個元素之陣列，為 [<經度>, <緯度>]
                    <Number>, <Number>
                  ]
                  "name" : <String>,          // 此中途點所在的道路名稱
                },
                ... (more results)...
              ],
              "routes" : [                    // 路徑規劃結果
                {
                  "legs" : [                  // 路徑規劃的每一段 `路程` (兩中途點為一段 `路程`。亦即，若只有起訖點，則 `legs` 只會有一個元素。而若有一個中途點，則將有兩個元素。依此類推)
                    {
                      "steps" : []
                      "distance" : <Number>   // 本段 `路程` 的距離 (單位為公尺)
                      "summary" : <String>    // 本段 `路程` 的摘要資訊
                      "duration" : <Number>   // 本段 `路程` 的估計旅行時間 (單位為秒)
                    }
                  ],
                  "geometry" : {
                    "coordinates" : [Array],  // 為一陣列，每個元素帶有兩個元素之陣列，分別為經度與緯度 (i.e., 此 `geometry` 陣列的每個元素為 [<經度>,<緯度>])。用以於地圖之圖面上繪製完整、美觀的路線圖
                    "type": "LineString"
                  },
                  "distance" : <Number>,      // 本路徑規劃的總距離 (單位為公尺)
                  "duration" : <Number>       // 本路徑規劃的總旅行時間 (單位為秒)
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

- **Example** : 此例以 `car` (汽車) 作為交通工具 (請於瀏覽器直接打開)

    ```
    HTTP GET "https://api.map8.zone/route/car/121.574494,25.075904;121.576499,25.068178;121.579343,25.068134.json?key=<您的 key>"
    ```
    ```json
    {
        "routes": [
            {
                "geometry": {
                    "coordinates": [
                        [121.574564, 25.075867],
                        [121.574525, 25.075806],
                        [121.574466, 25.075726],
                        ... (此為經緯度座標陣列, 供繪製地圖之用; 略)...
                    ],
                    "type": "LineString"
                },
                "legs": [
                    { "steps": [], "distance": 1244, "duration": 204, "summary": "港墘路, 舊宗路二段" },
                    { "steps": [], "distance": 406, "duration": 63, "summary": "瑞湖街, 民權東路六段11巷" }
                ],
                "distance": 1650,
                "duration": 267
            }
        ],
        "waypoints": [
            { "distance": 8, "name": "港墘路", "location": [121.574564, 25.075867] },
            { "distance": 0, "name": "瑞湖街", "location": [121.576499, 25.068178] },
            { "distance": 0, "name": "民權東路六段15巷", "location": [121.579343, 25.068134] }
        ]
    }
    ```

- [back to index](#api-index)

----

<p align="center">
<a href="https://map8.zone"><img src="../../images/logo_96x96.png" /></a> <br/> https://map8.zone
</p>
