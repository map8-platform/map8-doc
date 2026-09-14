# 台灣圖霸 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸 | Map8 Platform** 地圖平台

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#api-address-standardization-api

## Version
- v3.1_2025-09-19 (the present document)
    - 增加地址中翻英功能 (formatted_address_en ...等英文欄位)。

## [Address Validation] 地址正規化 / 地址校正 / 地址勘誤
功能 : 地址正規化 / 地址校正 / 地址勘誤

## API Index
- [Address Standardization API (地址正規化)](#address-standardization-api)

### Address Standardization API
台灣圖霸之 PAPAGO! 商用地圖擁有全台灣 988 萬筆門牌資料，精確到樓層！ 台灣圖霸，擁有全台灣超過 3000 萬筆新舊門牌資料庫，時時同步更新政府戶政司資料，提供最新地址現況。

> 請留意 : URL 必須正確 [編碼](https://en.wikipedia.org/wiki/Percent-encoding)，並且所有 Map8 API 均限制在最多 8192 個字元。當您建立呼叫 Map8 API 的 URL 時，請務必留意到此限制。

- **API** :

    ```
    https://api.map8.zone/v2/address/standardization/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **query**
            - 輸入您欲校正的地址。
        - **en**
            - 選擇性參數 : 是否需要回傳英文版欄位 (`true` / `false`; 預設為 `false`; 請注意 `true` / `false` 值以如字面 (string literal) 帶入，而非以 1 / 0 或其它字元帶入)。
- **Request Message Body** : None.
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
        - **Response Message Body**
            - Content-type: application/json
            - 回傳的結構的各個欄位的資訊所代表之意義如后

            ```
            {
                "html_attribution": [
                    "台灣圖霸",
                    "研鼎智能",
                    "PAPAGO!"
                ],
                "results": [
                    {
                        "formatted_address": <String>,    // 校正後地址
                        "formatted_address_en": <String>, // 校正後地址英文，en=true 才會顯示
                        "doorplateID": <String>,          // 系統內部使用之代碼，可忽略
                        "postcode3": <String>,            // 3 碼郵遞區號
                        "postcode33": <String>,           // 6 碼郵遞區號
                        "postcode32": <String>,           // 5 碼郵遞區號
                        "city": <String>,                 // 縣市
                        "city_en": <String>,              // 縣市英文，en=true 才會顯示
                        "town": <String>,                 // 鄉鎮市區
                        "town_en": <String>,              // 鄉鎮市區英文，en=true 才會顯示
                        "village": <String>,              // 村里
                        "village_en": <String>,           // 村里英文，en=true 才會顯示
                        "lin": <String>,                  // 鄰
                        "road": <String>,                 // 路名
                        "road_en": <String>,              // 路名英文，en=true 才會顯示
                        "hamlet": <String>,               // 地名
                        "hamlet_en": <String>,            // 地名英文，en=true 才會顯示
                        "lane": <String>,                 // 巷
                        "lane_en": <String>,              // 巷英文，en=true 才會顯示
                        "alley": <String>,                // 弄
                        "alley_en": <String>,             // 弄英文，en=true 才會顯示
                        "lon": <String>,                  // 衖衕
                        "num": <String>,                  // 門牌號
                        "num_en": <String>,               // 門牌號英文，en=true 才會顯示
                        "floor": <String>,                // 樓層
                        "floor_en": <String>,             // 樓層英文，en=true 才會顯示
                        "numAttr": <String>,              // 門牌屬性
                        "residenceID": <String>,          // 系統內部使用之代碼，可忽略
                        "compType": <String>,             // 編訂類型
                        "compDate": <String>,             // 編定日期
                        "trxDate": <String>,              // 異動日期
                        "geom": {
                            "type": "Point",
                            "coordinates": [Array]        // 座標資訊 [<經度>, <緯度>]
                        },
                        "resultAnalysis": {
                            "statusCode": [Array],        // 校正結果分析代碼
                            "redundantTextInQuery": <String> // 多餘字元
                        },
                        "history": []                     // 變更歷史
                    }
                ],
                "queryQuality": {
                    "statusCode": [Array]                 // 輸入地址品質分析代碼 (系統內部使用之代碼，可忽略)
                },
                "status": <String>                        // Status Code
            }
            ```
            - 上述每一筆搜尋結果內的各欄位, 若無值, 仍一律回傳, 但帶空值
            - **statusCode** : 校正結果分析代碼，回傳值意義如下，可能為多個值 :

                | 值 | 意義 |
                |---|---|
                | `0` | 地址及行政區完全符合, 且輸入的查詢中無多餘字元 |
                | `1` | 地址及行政區完全符合, 但輸入的查詢中有多餘字元 |
                | `2` | 「縣市」經校正 |
                | `3` | 「鄉鎮區」經校正 |
                | `4` | 「路街」經校正 |
                | `5` | 「地區」經校正 |
                | `6` | 「巷」經校正 |
                | `7` | 「弄」經校正 |
                | `8` | 「衖」經校正 |
                | `9` | 「號」經校正 (含臨、附) |
                | `10` | 「樓」經校正 (含臨、附) |
                | `11` | 地址「縣市」經校正，但屬升格 |
                | `12` | 地址「區」經校正，但屬升格 |
                | `13` | 門牌有變更 (*註1) |

            - *註1 : 此值主要是用來指出該筆地址有變更過, 並非暗示該筆為最新或最舊. 請由 `history` 欄位 (由舊而新排序) 讀取此住所的門牌變更紀錄, 與最新門牌.
    - Status code : **400** Bad Request
        - 表示您的 request 系統偵測到有錯誤而無法完成您的要求。通常是給入的參數多了或少了，或是格式有錯誤，或必要參數卻沒給，等等
        - **Response Message Body**
            - Content-type: application/json

            ```
            {
              "status" : <String>     // Status Code
            }
            ```
    - 參見 [HTTP Status Code](../appendix.md#http-status-code) 一節說明本 API 回傳值之一般通則
    - 參見 ["status" 欄位](../appendix.md#status-欄位) 一節說明 "status" 欄位的一般性意義

- **Example**

    ```
    HTTP GET "https://api.map8.zone/v2/address/standardization?key=<您的 key>&query=北市內湖區港墘路200號4樓之三&en=true"
    ```
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "臺北市內湖區港墘里港墘路200號四樓之３",
                "formatted_address_en": "4-3 F, No. 200, Gangqian Rd., Gangqian Vil., Neihu Dist., Taipei City",
                "doorplateID": "2e35e1af-d0ad-4571-ab8f-c0e8dac69b0a",
                "postcode3": "114",
                "postcode33": "114067",
                "postcode32": "11494",
                "city": "臺北市",
                "city_en": "Taipei City",
                "town": "內湖區",
                "town_en": "Neihu Dist.",
                "village": "港墘里",
                "village_en": "Gangqian Vil.",
                "lin": "20",
                "road": "港墘路",
                "road_en": "Gangqian Rd.",
                "hamlet": "",
                "hamlet_en": "",
                "lane": "",
                "lane_en": "",
                "alley": "",
                "alley_en": "",
                "lon": "",
                "numRaw": "２００號四樓之３",
                "num": "200號",
                "num_en": "No. 200",
                "floor": "四樓之３",
                "floor_en": "4-3 F",
                "numAttr": "",
                "residenceID": "ceadf149-e7fd-4d39-b0e2-fe72c674c098",
                "compType": "",
                "compDate": "",
                "trxDate": "",
                "geom": {
                    "type": "Point",
                    "coordinates": [121.574365, 25.075977]
                },
                "resultAnalysis": {
                    "statusCode": [2],
                    "redundantTextInQuery": "北市"
                },
                "history": []
            }
        ],
        "queryQuality": {
            "statusCode": [11]
        },
        "status": "OK"
    }
    ```

- [back to index](#api-index)

----

<p align="center">
<a href="https://map8.zone"><img src="../../images/logo_96x96.png" /></a> <br/> https://map8.zone
</p>
