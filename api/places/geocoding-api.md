# 台灣圖霸電子地圖 API 平台 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸電子地圖 API 平台 | Map8 Platform**

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#places-2

## Version
- v3.1_2025-09-19 (the present document)

## [Places] 地址定位與反定位
功能 :
- 地址定位 (geocoding, 也就是將 `地址 / 門牌` 轉為地理座標 `經緯度`)
- 與
- 反地址定位 (reverse geocoding, 反過來將地理座標 `經緯度` 轉為 `地址 / 門牌`)

## API Index
- [Geocoding API (地址定位 / 反地址定位)](#geocoding-api)

### Geocoding API

> 請注意 : `Geocoding API` 本次進版號，URL 路徑進版為 `/v2`/place/geocode。

> 請注意 : 透過 `Geocoding API` 進行查詢的 `address` 參數之內容，請務必盡可能格式化完整、清楚。譬如，`段` 以文字寫，而 `號` 以數字寫。文字之間無需空白。並正確冠以行政區，例如 `台北市中山區`。舉例來說，`南投縣水里鄉中山路一段210之3號之1` 是個最佳的寫法。格式化得越良好，越對準上例，則查詢結果將越精確。

- **API** :

    ```
    https://api.map8.zone/v2/place/geocode/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<輸出格式>**
        - 選擇性參數，本版本不理會此參數，response 一律以 JSON 格式回應。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **address**
            - 欲轉換為 `經緯度` 座標之 `地址` (若欲執行的是 `地址定位` ，則此參數為必須)。
        - **latlng**
            - 欲轉換為 `地址` 的 `經緯度` 座標 (格式為 `<緯度>,<經度>`) (若欲執行的是 `反地址定位` ，則此參數為必須)。
        - **postcode**
            - 選擇性參數 : 是否需要回傳三碼郵遞區號 (`true` / `false`; 預設為 `false`; 請注意 `true` / `false` 值以如字面 (string literal) 帶入，而非以 1 / 0 或其它字元帶入)。
        - **formatted_address_embed_postcode**
            - 若上述 `postcode` 參數有被指定，則指定此參數以將三碼郵遞區號直接內嵌於 response 之 `formatted_address` 欄位內 (否則，三碼郵遞區號將另以 `postcode` 欄位回傳)。
    - **(Migration 指南) 與 Google Maps 的 Geocoding API 相容性**
        1. 除以上 **參數** 章節所描述的參數外，其他 [Google Maps Geocoding API 的參數](https://developers.google.com/maps/documentation/geocoding/intro#geocoding) 包括 `components`, `bounds`, `language`, `region` 均 ignore (因此，同樣地，您可自行決定要刪除或留著)。
        2. **(Migration 指南) 與 Google Maps 的 Reverse Geocoding API 相容性**
            1. 除以上 **參數** 章節所描述的參數外，其他 [Google Maps Reverse Geocoding API 的參數](https://developers.google.com/maps/documentation/geocoding/intro#ReverseGeocoding) 包括 `language`, `result_type`, `location_type` 均 ignore (因此，同樣地，您可自行決定要刪除或留著)。
- **Request Message Body** : None.
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
    - `地址定位`
        - **Response Message Body**
            - Content-type: application/json

            ```
            {
              "html_attributions" : [                 // 您必須向使用者表彰之本 API 所屬的圖資版權資訊
                "台灣圖霸",
                "研鼎智能",
                "PAPAGO!"
              ],
              "results" : [
                {
                  "formatted_address" : <String>,   // 地址 (經整理、格式化過的)
                  "geometry" : {
                    "location" : {
                      "lat" : <Number>,            // 緯度
                      "lng" : <Number>             // 經度
                    },
                  },
                  "id" : <String>,                  // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                  "place_id" : <String>,            // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                  "name" : <String>,                // 本筆資料的名稱 (地名、道路名、地點名) (例如 "圖霸科技股份有限公司")
                  "city" : <String>,                // 本筆資料所屬的城市 (例如 "台北市")
                  "town" : <String>,                // 本筆資料所屬的行政區 (例如 "內湖區")
                  "type" : <String>,                // 本筆資料的類型，可為 : "地點"、"地址"、或 "道路"
                  "postcode": <String>,             // 本筆資料的三碼郵遞區號
                  "level": <String>,                // 本筆資料定位結果之層級，請見底下說明
                  "likelihood": <Number>,           // 本筆資料定位結果之信心指數，請見底下說明
                  "authoritative": <String>         // 本筆資料定位結果是否出自於 Map8 最具正規權威性的地址門牌資料庫。若是，本欄位值為 `true`，否則為 `false`
                }
              ],
              "status" : <String>  // Status Code
            }
            ```
            - 上述每一筆搜尋結果內的各欄位, 若無值, 仍一律回傳, 但帶空值
            - **(Migration 指南) 與 Google Maps 的 Geocoding API 相容性**
                1. 本 API 的 response 不會回傳任何 Google Maps Geocoding API 內的 optional 欄位包括 `address_components`, `geometry.location_type`, `geometry.viewport`, `types` 等欄位. 由於這些欄位原本即為 optional, 因此, suppose 不會對於您的 migration 造成影響
            - **level** : 定位結果的層級，可為 :

                | 值 | 意義 |
                |---|---|
                | `6` | 縣市 |
                | `5` | 鄉鎮市區 |
                | `4` | 路、路段 |
                | `3` | 巷 |
                | `2` | 弄 |
                | `1` | 號、之號 |
                | `0` | 無法定位 |
                | `-1` | 同號，"之號"內插，如10-3及10-8，內插出10-5的坐標 |
                | `-2` | 同單雙號內插，如 4號及10號內插成8號，或3號、11號內插出7號 |
                | `-3` | 不同單雙號內插，即4號、11號內插6號 |
                | `-4` | 同號、不同之號的代表點，如以10-3的坐標當做10-5的坐標 |
                | `-5` | 同單雙號的代表點，如以 4號的坐標當做10號的坐標 |
                | `-6` | 不同單雙號的代表點，如以 4號的坐標當做7號的坐標 |
                | `-7` | 同樣路名的其中一點，其他狀況，以同路名的第一門牌坐標為代表 |
                | `fuzzy` | 地址定位結果乃透過模糊搜尋結果而得 |

            - **likelihood** : 定位結果之信心指數。為百分比，數值為介於 0 ~ 100 之間帶兩位小數。本欄位僅在於 **level** 為 `fuzzy` 時有效。否則值為 `-1`。
            - **authoritative** : 定位結果是否出自於 Map8 最具正規 **權威性** 的 **地址門牌資料庫**。若是，本欄位值為 `true`，否則為 `false`。

            > 請注意 : 當定位結果之 `level` 小於 0 時 (i.e., 為推算值)，`place_id` 與 `id` 欄位均將為空值。

    - `反地址定位`
        - **Response Message Body**
            - Content-type: application/json

            ```
            {
              "html_attributions" : [                 // 您必須向使用者表彰之本 API 所屬的圖資版權資訊
                "台灣圖霸",
                "研鼎智能",
                "PAPAGO!"
              ],
              "results" : [                           // `搜尋結果` 陣列
                {
                  "formatted_address" : <String>,   // 地址 (經整理、格式化過的)
                  "name" : <String>,                // 本筆資料的名稱 (地名、道路名、地點名) (例如 "圖霸科技股份有限公司")
                  "city" : <String>,                // 本筆資料所屬的城市 (例如 "台北市")
                  "town" : <String>,                // 本筆資料所屬的行政區 (例如 "內湖區")
                  "postcode" : <String>,            // 本筆地址之三碼郵遞區號
                }
              ],
              "status" : <String>  // Status Code
            }
            ```
            - 上述每一筆搜尋結果內的各欄位, 若無值, 仍一律回傳, 但帶空值
            - **(Migration 指南) 與 Google Maps 的 Reverse Geocoding API 相容性**
                1. 本 API 的 response 不會回傳任何 Google Maps Reverse Geocoding API 內的 optional 欄位包括 `address_components`, `geometry.location_type`, `geometry.viewport`, `types` 等欄位. 由於這些欄位原本即為 optional, 因此, suppose 不會對於您的 migration 造成影響
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

- **Example** : `地址定位` (帶入 `address` 參數)

    ```
    HTTP GET "https://api.map8.zone/v2/place/geocode/json?key=<您的 key>&address=台北市內湖區港墘路200號"
    ```
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "台北市內湖區港墘路200號",
                "geometry": { "location": { "lat": 25.0759040326, "lng": 121.574494301 } },
                "id": "IykqAAIDRhYCWFpTQj9eUSE7PBZUThABOwtXcn5SHxYiDzsFflo0Eg==",
                "place_id": "IykqAAIDRhYCWFpTQj9eUSE7PBZUThABOwtXcn5SHxYiDzsFflo0Eg==",
                "name": "港墘路200號",
                "city": "台北市",
                "town": "內湖區",
                "type": "地址",
                "postcode": "114",
                "level": "1",
                "likelihood": -1,
                "authoritative": "true"
            }
        ],
        "status": "OK"
    }
    ```
    - 可以看到 `"level": "1"` 表示定位結果精確到門牌 (`號`)
    - 此時，因為並非是模糊搜尋而得，因此 `"likelihood": -1`
    - 而由於精確的定位結果到門牌，乃出自於 Map8 平台最具權威的門牌地址資料庫，因此，`authoritative` 顯示為 `true`

- **Example** : `地址定位`，模糊搜尋的例子 (`address=太平村下坑6號`)

    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "嘉義縣梅山鄉太平村下坑6號",
                "geometry": { "location": { "lat": 23.562837, "lng": 120.596439 } },
                "id": "NzYqAQYCTRcKV19TBgx6KTEbMSF/ARhXTysDdUxVX0AxIyR6Y1wCEg==",
                "place_id": "NzYqAQYCTRcKV19TBgx6KTEbMSF/ARhXTysDdUxVX0AxIyR6Y1wCEg==",
                "name": "雲海大飯店",
                "city": "嘉義縣",
                "town": "梅山鄉",
                "type": "地點",
                "postcode": "603",
                "level": "fuzzy",
                "likelihood": 82.38,
                "authoritative": "false"
            }
        ],
        "status": "OK"
    }
    ```
    - 此地址實際上是個將錯就錯的例子 -- 台灣戶政司實際上查無此地址。此外，輸入的 `address` 參數非常破碎。
    - 儘管如此，Map8 平台系統仍盡可能查詢到了有該地址的景點登錄資料。因此，可以看到 `"level": "fuzzy"` 表示定位結果為搜尋而得，且信心指數 `likelihood` 超過 80%
    - 由於 `嘉義縣梅山鄉太平村下坑6號` 此一地址實際上並不存在，因此，`authoritative` 顯示為 `false`

- **Example** : `地址定位`，內插的例子 (`address=新北市板橋區金門街215巷76之3號`)

    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "新北市板橋區金門街215巷76-3號",
                "geometry": { "location": { "lat": 24.987083232098, "lng": 121.433119327778 } },
                "id": "IykqAAUETR8CXV1TOjlSDiAISBBNVCFlRiF9AF9uJUAmLx5FXlACEg==",
                "place_id": "IykqAAUETR8CXV1TOjlSDiAISBBNVCFlRiF9AF9uJUAmLx5FXlACEg==",
                "name": "金門街215巷26之3號",
                "city": "新北市",
                "town": "板橋區",
                "type": "地址",
                "postcode": "220",
                "level": "-2",
                "likelihood": -1,
                "authoritative": "false"
            }
        ],
        "status": "OK"
    }
    ```
    - 可以看到 `"authoritative": "false"`，表示定位結果之 `formatted_address` 並不存在於 Map8 平台最具權威的門牌地址資料庫內
    - 而 `"level": "-2"` 表示定位結果為同側雙號的內外插 (interpolation / extrapolation) 而得。也因為是透過內外插的定位結果，而非透過模糊搜尋，因此，`"likelihood": -1`

- **Example** : `反地址定位` (帶入 `latlng` 參數)

    ```
    HTTP GET "https://api.map8.zone/v2/place/geocode/json?key=<您的 key>&latlng=25.0759040326,121.574494301"
    ```
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "台北市內湖區港墘路200號",
                "name": "港墘路200號",
                "city": "台北市",
                "town": "內湖區"
            }
        ],
        "status": "OK"
    }
    ```

- **Example** : `反地址定位` (帶入 `latlng` 參數，並要求三碼郵遞區號)

    ```
    HTTP GET "https://api.map8.zone/v2/place/geocode/json?key=<您的 key>&latlng=25.0759040326,121.574494301&postcode=true"
    ```
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "台北市內湖區港墘路200號",
                "name": "港墘路200號",
                "city": "台北市",
                "town": "內湖區",
                "postcode": "114"
            }
        ],
        "status": "OK"
    }
    ```

- **Example** : `反地址定位` (帶入 `latlng` 參數，並要求三碼郵遞區號直接內嵌於 `formatted_address` 欄位)

    ```
    HTTP GET "https://api.map8.zone/v2/place/geocode/json?key=<您的 key>&latlng=25.0759040326,121.574494301&postcode=true&formatted_address_embed_postcode=true"
    ```
    ```json
    {
        "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
        "results": [
            {
                "formatted_address": "114 台北市內湖區港墘路200號",
                "name": "港墘路200號",
                "city": "台北市",
                "town": "內湖區"
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
