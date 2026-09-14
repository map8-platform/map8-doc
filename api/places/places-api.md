# 台灣圖霸電子地圖 API 平台 | Map8 Platform
# Application Programming Interface Specification
歡迎使用 **<img src="../../images/logo.png" width="28" height="28"> 台灣圖霸電子地圖 API 平台 | Map8 Platform**

> Authentication、Notation 與 Version 請參見 [README](../../README.md)。線上版文件 : https://www.map8.zone/map8-api-docs/#places

## Version
- v3.1_2025-09-19 (the present document)

## [Places] 地點搜尋
功能 : 搜尋台灣圖霸電子地圖 API 平台圖資內的地點

## API Index
- **Place Search** :
    - [Find Place API (給定關鍵詞，搜尋地點)](#find-place-api)
    - [Place Details API (取得地點詳細資訊)](#place-details-api)
    - [Nearby Search API (給定座標，搜尋周遭)](#nearby-search-api)
    - [Text Search API (以任意的關鍵詞組合進行搜尋)](#text-search-api)
- [Place Autocomplete API (讓使用者邊輸入，便邊回應出推測的可能清單)](#place-autocomplete-api)
- [Geocoding API (地址定位 / 反地址定位)](./geocoding-api.md)

<br/>

### Find Place API
給定關鍵詞，搜尋地點

> 請注意 : `Find Place API` 本次進版號，URL 路徑進版為 `/v2`/place/findplacefromtext。

- **API** :

    ```
    https://api.map8.zone/v2/place/findplacefromtext/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<輸出格式>**
        - 選擇性參數，本版本不理會此參數，response 一律以 JSON 格式回應。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **input**
            - 必要參數，欲搜尋的地點的關鍵詞 (無任何空白分隔)。可以是地址、地名、商店名稱、或電話。
        - **inputtype**
            - 選擇性參數，應為 `textquery` 或 `phonenumber` 兩者之一。若未給，則由台灣圖霸電子地圖 API 平台自動智慧判斷。
        - **locationbias**
            - 選擇性參數，可為 :
                - `ipbias` : 要求系統依照您發出此 request 的 IP address 來決定本 API 進行搜尋時的中心點。
                - `<緯度>,<經度>` : 要求系統依據給定的地理經緯度座標為搜尋中心點。
            - 此參數若未給，則系統預設採用 `ipbias`。
            - (注意) **採用 `ipbias` 方式將增加額外的系統動作，可能因網路而造成搜尋速度相當幅度的延遲。因此，建議您盡可能不要採用 `ipbias`。強烈建議您一律採 `locationbias=<緯度>,<經度>` 參數**
        - **postcode**
            - 選擇性參數 : 是否需要回傳三碼郵遞區號 (`true` / `false`; 預設為 `false`; 請注意 `true` / `false` 值以如字面 (string literal) 帶入，而非以 1 / 0 或其它字元帶入)。
        - **formatted_address_embed_postcode**
            - 若上述 `postcode` 參數有被指定，則指定此參數以將三碼郵遞區號直接內嵌於 response 之 `formatted_address` 欄位內 (否則，三碼郵遞區號將另以 `postcode` 欄位回傳)。
    - **(Migration 指南) 與 Google Maps 的 Find Place API 相容性**
        1. 台灣圖霸電子地圖 API 平台一次就把全部欄位回應給您，因此，[Google Maps Find Place API 的 fields 參數](https://developers.google.com/places/web-service/search#Fields) 台灣圖霸電子地圖 API 平台見到會直接 ignore -- 因此，如果您本來有使用此參數，那您可以選擇放著不改 (如果您認為這樣能降低您改動的 effort 的話)。
        2. `language`, `locationbias.circular`, `locationbias.rectangular` 也均 ignore (因此，同樣地，您可自行決定要刪除或留著)。
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
        - **Response Message Body**
            - Content-type: application/json

            ```
            {
              "html_attributions" : [                 // 您必須向使用者表彰之本 API 所屬的圖資版權資訊
                "台灣圖霸",
                "研鼎智能",
                "PAPAGO!"
              ],
              "candidates" : [                        // `搜尋結果` 陣列
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
                  "tel" : <String>,                 // 本筆資料的電話號碼 (例如 "02-87921567")
                  "city" : <String>,                // 本筆資料所屬的城市 (例如 "台北市")
                  "town" : <String>,                // 本筆資料所屬的行政區 (例如 "內湖區")
                  "type" : <String>,                // 本筆資料的類型，可為 : "地點", "地址", 或 "道路"
                  "chain" : <String>,               // 本筆資料若屬於某連鎖機構, 則此欄位為該機構名稱 (例如 "研勤集團")
                  "branch" : <String>,              // 本筆資料若屬於某連鎖機構, 則此欄位為該分店名稱 (例如 "台中辦公室")
                  "cat" : <String>,                 // 本筆資料於台灣圖霸電子地圖 API 平台內所歸屬的地點類型 (例如 "公司行號")
                  "distance" : <Number>,            // 本筆資料與輸入的中心點座標間的直線距離, 單位為 km
                }
              ],
              "status" : <String>  // Status Code
            }
            ```
            - 上述 `搜尋結果` 陣列中每一筆內的各欄位, 若無值, 仍一律回傳, 但帶空值
            - **(Migration 指南) 與 Google Maps 的 Find Place API 相容性**
                1. 本 API 的 response 不會回傳任何 [Google Maps Find Place API](https://developers.google.com/places/web-service/search#find-place-responses) 內的 optional 欄位包括 `geometry.viewport`, `opening_hours`, `photos`, `rating`, `debug_log` 等欄位. 由於這些欄位原本即為 optional, 因此, suppose 不會對於您的 migration 造成影響
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
    HTTP GET "https://api.map8.zone/v2/place/findplacefromtext/json?key=<您的 key>&input=圖霸科技&locationbias=25.06102,121.58790"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "result": {
        "formatted_address": "台北市內湖區港墘路200號4樓之3",
        "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
        "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "name": "圖霸科技股份有限公司",
        "tel": "02-87921567",
        "city": "台北市",
        "town": "內湖區",
        "type": "地點",
        "chain": "",
        "branch": "",
        "cat": "公司行號",
        "distance": 0.91
      },
      "status": "OK"
    }
    ```

- **Example** : 搜尋地點，並要求若搜尋結果有地址則附帶三碼郵遞區號

    ```
    HTTP GET "https://api.map8.zone/v2/place/findplacefromtext/json?key=<您的 key>&input=圖霸科技&locationbias=25.06102,121.58790&postcode"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "result": {
        "formatted_address": "台北市內湖區港墘路200號4樓之3",
        "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
        "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "name": "圖霸科技股份有限公司",
        "tel": "02-87921567",
        "city": "台北市",
        "town": "內湖區",
        "type": "地點",
        "chain": "",
        "branch": "",
        "cat": "公司行號",
        "distance": 0.91,
        "postcode": "114"
      },
      "status": "OK"
    }
    ```

- **Example** : 搜尋地點，並要求若搜尋結果有地址則附帶三碼郵遞區號內嵌於 `formatted_address` 欄位

    ```
    HTTP GET "https://api.map8.zone/v2/place/findplacefromtext/json?key=<您的 key>&input=圖霸科技&locationbias=25.06102,121.58790&postcode&formatted_address_embed_postcode"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "result": {
        "formatted_address": "114 台北市內湖區港墘路200號4樓之3",
        "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
        "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "name": "圖霸科技股份有限公司",
        "tel": "02-87921567",
        "city": "台北市",
        "town": "內湖區",
        "type": "地點",
        "chain": "",
        "branch": "",
        "cat": "公司行號",
        "distance": 0.91
      },
      "status": "OK"
    }
    ```

- [back to index](#api-index)

<br/>

### Place Details API
取得地點詳細資訊 (主要用於 [Autocomplete API](#place-autocomplete-api) -- 當使用者選擇了候選清單中的一項的時候，透過 `place_id` 呼叫本 API 以取得座標、電話等等詳細資料)。

> 請注意 : `Place Details API` 的 URL 路徑，與其他 Places API 統一共同為 `/v2`/place/details。

- **API** :

    ```
    https://api.map8.zone/v2/place/details/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<輸出格式>**
        - 選擇性參數，本版本不理會此參數，response 一律以 JSON 格式回應。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **placeid**
            - 必要參數，欲取得的地點之 ID (由之前呼叫之 台灣圖霸電子地圖 API 平台之其他 API 所回傳；例如 [Autocomplete API](#place-autocomplete-api))。

            > 提醒您 : 輸入之 `placeid` 請務必注意遵照標準規範 -- 務必先經過 [URL encode](https://en.wikipedia.org/wiki/Percent-encoding) 編碼後再傳入，以免發生錯誤而收到 HTTP 400 Bad Request。(事實上，URL 所有以 query string 形式帶入的參數，應當都要視情況，適當地經過 URL encode 編碼後再傳入)。

        - **postcode**
            - 選擇性參數 : 是否需要回傳三碼郵遞區號 (`true` / `false`; 預設為 `false`)。
        - **formatted_address_embed_postcode**
            - 若上述 `postcode` 參數有被指定，則指定此參數以將三碼郵遞區號直接內嵌於 response 之 `formatted_address` 欄位內 (否則，三碼郵遞區號將另以 `postcode` 欄位回傳)。
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
        - **Response Message Body**
            - Content-type: application/json

            ```
            {
              "html_attributions" : [                 // 您必須向使用者表彰之本 API 所屬的圖資版權資訊
                "台灣圖霸",
                "研鼎智能",
                "PAPAGO!"
              ],
              "result" : {
                "formatted_address" : <String>,   // 地址 (經整理、格式化過的)
                "geometry" : {
                  "location" : {
                    "lat" : <Number>,            // 緯度
                    "lng" : <Number>             // 經度
                  },
                },
                "id" : <String>,                  // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                "place_id" : <String>,            // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                "name" : <String>,                // 此地點的名稱 (地名、道路名、地點名) (例如 "圖霸科技股份有限公司")
                "tel" : <String>,                 // 此地點的電話號碼 (例如 "02-87921567")
                "city" : <String>,                // 此地點所屬的城市 (例如 "台北市")
                "town" : <String>,                // 此地點所屬的行政區 (例如 "內湖區")
                "type" : <String>,                // 此地點的類型，可為 : "地點", "地址", 或 "道路"
                "chain" : <String>,               // 此地點若屬於某連鎖機構, 則此欄位為該機構名稱 (例如 "研勤集團")
                "branch" : <String>,              // 此地點若屬於某連鎖機構, 則此欄位為該分店名稱 (例如 "台中辦公室")
                "cat" : <String>,                 // 此地點於台灣圖霸電子地圖 API 平台內所歸屬的地點類型 (例如 "公司行號")
                "distance" : <Number>,            // 本筆資料與輸入的中心點座標間的直線距離, 單位為 km
              },
              "status" : <String>  // Status Code
            }
            ```
            - 上述 `result` 內的各欄位, 若無值, 仍一律回傳, 但帶空值
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

- **Example** : 給定地點 ID 以取得地點詳細資訊 (同時，若該地點有地址，也取回三碼郵遞區號)

    ```
    HTTP GET "https://api.map8.zone/v2/place/details/json?key=<您的 key>&placeid=NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg%3D%3D&postcode=true"
    ```
    > 提醒您 : 上開範例的 `placeid` 參數之值 `NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg%3D%3D` 乃是將原始數值 `NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==` 透過 URL encode 後所獲得。

    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "result": {
        "formatted_address": "台北市內湖區港墘路200號4樓之3",
        "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
        "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
        "name": "圖霸科技股份有限公司",
        "tel": "02-87921567",
        "city": "台北市",
        "town": "內湖區",
        "type": "地點",
        "chain": "",
        "branch": "",
        "cat": "公司行號",
        "distance": 0,
        "postcode": "114"
      },
      "status": "OK"
    }
    ```

- [back to index](#api-index)

<br/>

### Nearby Search API
給定座標，搜尋周遭

> 請注意 : `Nearby Search API` 本次進版號，URL 路徑進版為 `/v2`/place/nearbysearch。

> 請注意 : 本版本新增 `cat` 參數，以支援欲搜尋的 [地點類型](../appendix.md#地點類型)。

- **API** :

    ```
    https://api.map8.zone/v2/place/nearbysearch/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<輸出格式>**
        - 選擇性參數，本版本不理會此參數，response 一律以 JSON 格式回應。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **location**
            - 必要參數，要求系統依據給定的地理座標為搜尋中心點。格式為 `<緯度>,<經度>`，分別為緯度與經度。
        - **radius**
            - 選擇性參數，指定以上述 `location` 為中心，以本參數 `radius` 指定方圓半徑之距離作為搜索範圍 (最大為 50.000 公里)。若未給，則由台灣圖霸電子地圖 API 平台自動智慧判斷。
        - **limit**
            - 選擇性參數。指定取回的資料筆數。單次回應最多為 100 筆。
        - **cat**
            - 選擇性參數。指定欲搜尋周遭之地點類型。請參考所支援之 [地點類型](../appendix.md#地點類型)。
            - 可多選 : 請直接列舉地點類型，透過逗號 (`,`) 加以分隔即可 (譬如 `cat=國小,國中,高中職校`)。
        - **postcode**
            - 選擇性參數 : 是否需要回傳三碼郵遞區號 (`true` / `false`; 預設為 `false`)。範例同 [Find Place API](#find-place-api) 之範例。
        - **formatted_address_embed_postcode**
            - 若上述 `postcode` 參數有被指定，則指定此參數以將三碼郵遞區號直接內嵌於 response 之 `formatted_address` 欄位內 (否則，三碼郵遞區號將另以 `postcode` 欄位回傳)。範例同 [Find Place API](#find-place-api) 之範例。
    - **(Migration 指南) 與 Google Maps 的 Nearby Search API 相容性**
        1. 除以上 **參數** 章節所描述的參數外，其他 [Google Maps Nearby Search API 的參數](https://developers.google.com/places/web-service/search#PlaceSearchRequests) 包括 `rankby`, `keyword`, `language`, `minprice`, `name`, `opennow`, `type`, `pagetoken` 也均 ignore (因此，同樣地，您可自行決定要刪除或留著)。
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
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
                  "geometry" : {
                    "location" : {
                      "lat" : <Number>,            // 緯度
                      "lng" : <Number>             // 經度
                    },
                  },
                  "id" : <String>,                  // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                  "place_id" : <String>,            // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                  "name" : <String>,                // 本筆資料的名稱 (地名、道路名、地點名) (例如 "圖霸科技股份有限公司")
                  "tel" : <String>,                 // 本筆資料的電話號碼 (例如 "02-87921567")
                  "city" : <String>,                // 本筆資料所屬的城市 (例如 "台北市")
                  "town" : <String>,                // 本筆資料所屬的行政區 (例如 "內湖區")
                  "type" : <String>,                // 本筆資料的類型，可為 : "地點", "地址", 或 "道路"
                  "chain" : <String>,               // 本筆資料若屬於某連鎖機構, 則此欄位為該機構名稱 (例如 "研勤集團")
                  "branch" : <String>,              // 本筆資料若屬於某連鎖機構, 則此欄位為該分店名稱 (例如 "台中辦公室")
                  "cat" : <String>,                 // 本筆資料於台灣圖霸電子地圖 API 平台內所歸屬的地點類型 (例如 "公司行號")
                  "distance" : <Number>,            // 本筆資料與輸入的中心點座標間的直線距離, 單位為 km
                },
                ... (more results) ...
              ],
              "status" : <String>  // Status Code
            }
            ```
            - 上述每一筆搜尋結果內的各欄位, 若無值, 仍一律回傳, 但帶空值
            - **results** 陣列帶回本次搜尋的查詢結果。本 API 的查詢，最多一次回應 100 筆資料
            - **(Migration 指南) 與 Google Maps 的 Nearby Search API 相容性**
                1. 本 API 的 response 不會回傳 [Google Maps Nearby Search API](https://developers.google.com/places/web-service/search#nearby-search-and-text-search-responses) 內 `results[i]` 的 optional 欄位包括 `icon`, `opening_hours`, `photos`, `scope`, `alt_ids`, `reference`, `types`, `vicinity` 等欄位. 由於這些欄位原本即為 optional, 因此, suppose 不會對於您的 migration 造成影響
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

- **Example** : 搜尋以地點為中心之周遭的便利商店與學校 (註 : 為減少篇幅，底下僅列出前兩筆)

    ```
    HTTP GET "https://api.map8.zone/v2/place/nearbysearch/json?key=<您的 key>&location=25.075904,121.574494&cat=便利商店,國小,國中,高中職校&postcode=true"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "results": [
        {
          "formatted_address": "台北市內湖區瑞光路302號1樓",
          "geometry": { "location": { "lat": 25.076494, "lng": 121.574273 } },
          "id": "NzYqAQYFRB4KVl9TMF5nJQgoNB1PUiNaGjIFRgpiDBZISQMHA2AGEg==",
          "place_id": "NzYqAQYFRB4KVl9TMF5nJQgoNB1PUiNaGjIFRgpiDBZISQMHA2AGEg==",
          "name": "全家便利商店瑞亞店",
          "tel": "02-77287636",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "chain": "全家便利商店",
          "branch": "瑞亞店",
          "cat": "便利商店",
          "distance": 0.069,
          "postcode": "114"
        },
        {
          "formatted_address": "台北市內湖區江南街128號",
          "geometry": { "location": { "lat": 25.076234, "lng": 121.575543 } },
          "id": "NzYqAQYCRhwDX1NTNh5+KTJOMwByZxxxDzpdewlEFiFWLTsHbF4CEg==",
          "place_id": "NzYqAQYCRhwDX1NTNh5+KTJOMwByZxxxDzpdewlEFiFWLTsHbF4CEg==",
          "name": "7-ELEVEN瑞湖門市",
          "tel": "02-26588016",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "chain": "7-ELEVEN",
          "branch": "瑞湖門市",
          "cat": "便利商店",
          "distance": 0.112,
          "postcode": "114"
        }
      ],
      "status": "OK"
    }
    ```

- **Example** : 不指定 `cat` (地點類型) 參數 (註 : 為減少篇幅，底下僅列出前兩筆)

    ```
    HTTP GET "https://api.map8.zone/v2/place/nearbysearch/json?key=<您的 key>&location=25.06102,121.58790"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "results": [
        {
          "formatted_address": "台北市內湖區港墘路200號",
          "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
          "id": "NzYqAQYHTB0FWFhTHQZ+TBEoFhV7Qg9iOi1fYQ1FDy9VKTRmQwEeEg==",
          "place_id": "NzYqAQYHTB0FWFhTHQZ+TBEoFhV7Qg9iOi1fYQ1FDy9VKTRmQwEeEg==",
          "name": "景睿科技股份有限公司",
          "tel": "02-26562508",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "chain": "",
          "branch": "",
          "cat": "公司行號",
          "distance": 0
        },
        {
          "formatted_address": "台北市內湖區港墘路200號4樓之3",
          "geometry": { "location": { "lat": 25.075904, "lng": 121.574494 } },
          "id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
          "place_id": "NzYqAQYARhkCVltTRRxiJBIYMDIIZgVSLyMdf25eBDIIPQ1VHWEwEg==",
          "name": "圖霸科技股份有限公司",
          "tel": "02-87921567",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "chain": "",
          "branch": "",
          "cat": "公司行號",
          "distance": 0
        }
      ],
      "status": "OK"
    }
    ```

- [back to index](#api-index)

<br/>

### Text Search API
以任意的關鍵詞組合進行搜尋

> 請注意 : `Text Search API` 本次進版號，URL 路徑進版為 `/v2`/place/textsearch。

> 請注意 : 本版本新增 `cat` 參數，以支援欲搜尋的 [地點類型](../appendix.md#地點類型)。

- **API** :

    ```
    https://api.map8.zone/v2/place/textsearch/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<輸出格式>**
        - 選擇性參數，本版本不理會此參數，response 一律以 JSON 格式回應。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **query**
            - 必要參數，給定任意的關鍵詞組合 (請把這個當作搜尋引擎) 關鍵詞以空白分隔, 例如 "加油站 台中" 這樣即可得到滿意的結果。
        - **cat**
            - 選擇性參數。指定欲搜尋之地點類型。請參考所支援之 [地點類型](../appendix.md#地點類型)。
            - 可多選 : 請直接列舉地點類型，透過逗號 (`,`) 加以分隔即可 (譬如 `cat=國小,國中,高中職校`)。
        - **location**
            - 選擇性參數，要求系統依據給定的地理座標為搜尋中心點。格式為 `<緯度>,<經度>`，分別為緯度與經度。
            - 此參數若未給，則系統預設依照您發出此 request 的 IP address 來決定本 API 進行搜尋時的中心點。但請注意 : **此方式將增加額外的系統動作，可能因網路而造成搜尋速度相當幅度的延遲。因此，強烈建議您一律給 `location=<緯度>,<經度>` 參數**
        - **radius**
            - 選擇性參數，指定以上述 `location` 為中心，以本參數 `radius` 指定方圓半徑之距離作為搜索範圍 (最大為 50.000 公里)。若未給，則由台灣圖霸電子地圖 API 平台自動智慧判斷。
        - **postcode**
            - 選擇性參數 : 是否需要回傳三碼郵遞區號 (`true` / `false`; 預設為 `false`)。範例同 [Find Place API](#find-place-api) 之範例。
        - **formatted_address_embed_postcode**
            - 若上述 `postcode` 參數有被指定，則指定此參數以將三碼郵遞區號直接內嵌於 response 之 `formatted_address` 欄位內 (否則，三碼郵遞區號將另以 `postcode` 欄位回傳)。範例同 [Find Place API](#find-place-api) 之範例。
    - **(Migration 指南) 與 Google Maps 的 Text Search API 相容性**
        1. 除以上 **參數** 章節所描述的參數外，其他 [Google Maps Text Search API 的參數](https://developers.google.com/places/web-service/search#TextSearchRequests) 包括 `region`, `language`, `minprice`, `opennow`, `type`, `pagetoken` 也均 ignore (因此，同樣地，您可自行決定要刪除或留著)。
- **Response**
    - 同 Nearby Search, 請參見 [Nearby Search API](#nearby-search-api) 的 **Response** 章節。

- **Example** : 以 `內湖 台北` 為關鍵字搜尋地點類型為 `加油站` 者 (註 : 為減少篇幅，底下僅列出前兩筆)

    ```
    HTTP GET "https://api.map8.zone/v2/place/textsearch/json?key=<您的 key>&query=內湖 台北&cat=加油站&location=25.06102,121.58790"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "results": [
        {
          "formatted_address": "台北市內湖區新明路92號",
          "geometry": { "location": { "lat": 25.059615, "lng": 121.589523 } },
          "id": "NzYqAQYCRh0BXVNTPQV9CxcoDzIMcwtXHzkGU3dYEQ8TDRF8WhlBEg==",
          "place_id": "NzYqAQYCRh0BXVNTPQV9CxcoDzIMcwtXHzkGU3dYEQ8TDRF8WhlBEg==",
          "name": "統一速邁樂加油站內湖一站",
          "tel": "02-27929031",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "chain": "統一速邁樂加油站",
          "branch": "內湖一站",
          "cat": "加油站",
          "distance": 0.226
        },
        {
          "formatted_address": "台北市內湖區民權東路六段50號",
          "geometry": { "location": { "lat": 25.068355, "lng": 121.583351 } },
          "id": "NzYqAQYCRh0BXV9TMSMOEx0+STRRfQ9oWFNcawFEMSALDyMGdFkWEg==",
          "place_id": "NzYqAQYCRh0BXV9TMSMOEx0+STRRfQ9oWFNcawFEMSALDyMGdFkWEg==",
          "name": "中油加油站內湖站",
          "tel": "02-27920678",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "chain": "中油加油站",
          "branch": "內湖站",
          "cat": "加油站",
          "distance": 0.936
        }
      ],
      "status": "OK"
    }
    ```

- [back to index](#api-index)

<br/>

### Place Autocomplete API
讓使用者邊輸入，便邊回應出推測的可能清單 -- 運用在逐字逼近搜尋目標物的場景上。

> 請注意 : 如同 Google，本 `Autocomplete API` 不會帶回詳細資料 (座標、電話等等)。請您以取得的 `place_id` 呼叫 [Place Details API](#place-details-api) 做為 Autocomplete API 的結尾，並獲取您所選擇之地點的詳細資料。

- **API** :

    ```
    https://api.map8.zone/v2/place/autocomplete/<輸出格式>?<參數>
    ```
- **HTTP Method** :
    - **GET**
- **Synopsis**
    - **<輸出格式>**
        - 選擇性參數，本版本不理會此參數，response 一律以 JSON 格式回應。
    - **<參數>**
        - **key**
            - 必要參數，請帶進您的 key。
        - **input**
            - 必要參數，欲搜尋的關鍵詞 (無任何空白分隔) -- 通常是輸入欄當下的使用者輸入。例如 `捷運大坪`。可以是地址、地名、商店名稱、或電話。
        - **type**
            - 選擇性參數 : 回傳清單類型為 `地址` 或 `地點`，預設為兩者混合搜尋
        - **location**
            - 選擇性參數，要求系統依據給定的地理座標為搜尋中心點。格式為 `<緯度>,<經度>`，分別為緯度與經度。
            - 此參數若未給，則系統預設依照您發出此 request 的 IP address 來決定本 API 進行搜尋時的中心點。但請注意 : **此方式將增加額外的系統動作，可能因網路而造成搜尋速度相當幅度的延遲。因此，強烈建議您一律給 `location=<緯度>,<經度>` 參數**
        - **radius**
            - 選擇性參數，指定以上述 `location` 為中心，以本參數 `radius` 指定方圓半徑之距離作為搜索範圍 (最大為 50.000 公里)。若未給，則由台灣圖霸電子地圖 API 平台自動智慧判斷。
        - **strictbounds**
            - 選擇性參數 : 是否要求強制遵循上述 `radius` 參數之指定範圍 (`true` / `false`; 預設為 `false`)。
    - **(Migration 指南) 與 Google Maps 的 Place Autocomplete API 相容性**
        1. 除以上 **參數** 章節所描述的參數外，其他 [Google Maps Place Autocomplete API 的參數](https://developers.google.com/places/web-service/autocomplete#place_autocomplete_requests) 包括 `sessiontoken`, `offset`, `language`, `types`, `components` 也均 ignore (因此，同樣地，您可自行決定要刪除或留著)。
- **Request Message Body** : None.
- **Response**
    - Status code : **200** OK
        - 表示成功完成您的 request
        - **Response Message Body**
            - Content-type: application/json

            ```
            {
              "html_attributions" : [                 // 您必須向使用者表彰之本 API 所屬的圖資版權資訊
                "台灣圖霸",
                "研鼎智能",
                "PAPAGO!"
              ],
              "predictions" : [                       // `搜尋結果` 陣列
                {
                  "id" : <String>,                  // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                  "place_id" : <String>,            // 此地點於台灣圖霸電子地圖 API 平台內的地點 ID
                  "name" : <String>,                // 本筆資料的名稱 (地名、道路名、地點名) (例如 "圖霸科技股份有限公司")
                  "city" : <String>,                // 本筆資料所屬的城市 (例如 "台北市")
                  "town" : <String>,                // 本筆資料所屬的行政區 (例如 "內湖區")
                  "type" : <String>,                // 本筆資料的類型，可為 : "地點", "地址", 或 "道路"
                  "cat" : <String>,                 // 本筆資料於台灣圖霸電子地圖 API 平台內所歸屬的地點類型 (例如 "公司行號")
                  "distance" : <Number>,            // 本筆資料與輸入的中心點座標間的直線距離, 單位為 km
                },
                ... (more results) ...
              ],
              "status" : <String>  // Status Code
            }
            ```
            - 上述每一筆搜尋結果內的各欄位, 若無值, 仍一律回傳, 但帶空值
            - **predictions** 陣列帶回本次的預測結果。本 API 的查詢，最多將一次回應多達 10 筆資料
            - **請注意** : 如同 Google，本 `Autocomplete API` 不會帶回詳細資料 (座標、電話等等)。請您以取得的 `place_id` 呼叫 [Place Details API](#place-details-api) 做為 Autocomplete API 的結尾，並獲取您所選擇之地點的詳細資料
            - **(Migration 指南) 與 Google Maps 的 Place Autocomplete API 相容性**
                1. 本 API 的 response 與 [Google Maps Place Autocomplete API](https://developers.google.com/places/web-service/autocomplete#place_autocomplete_responses) 內 `predictions[i]` 內的欄位截然不同 (包括 `description`, `matched_substrings`, `reference`, `terms`, `types`, `structured_formatting` 等欄位)。因此，本 API 的 migration 功夫可能會稍大些
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

- **Example** (註 : 為減少篇幅，底下僅列出前三筆)

    ```
    HTTP GET "https://api.map8.zone/v2/place/autocomplete/json?key=<您的 key>&input=明美&location=25.06102,121.58790"
    ```
    ```json
    {
      "html_attribution": ["台灣圖霸", "研鼎智能", "PAPAGO!"],
      "status": "OK",
      "predictions": [
        {
          "id": "NzYqAQYGQhoHWlpTOi0OLlIbDkdAZR0DAA5+UAtYCxwgNhxdRVggEg==",
          "place_id": "NzYqAQYGQhoHWlpTOi0OLlIbDkdAZR0DAA5+UAtYCxwgNhxdRVggEg==",
          "name": "明美",
          "city": "台北市",
          "town": "松山區",
          "type": "地點",
          "cat": "購物商場",
          "distance": 2.212
        },
        {
          "id": "NzYqAQYEQR8LWVtTQDwZFgkVPTZVekVeMy5VQUkfXiggTCRbDngkEg==",
          "place_id": "NzYqAQYEQR8LWVtTQDwZFgkVPTZVekVeMy5VQUkfXiggTCRbDngkEg==",
          "name": "明美公園",
          "city": "台北市",
          "town": "內湖區",
          "type": "地點",
          "cat": "公園",
          "distance": 0.076
        },
        {
          "id": "NzYqAQYCRxsDWl5TDSlXFgABEEJ+dThAMBsPcFkGSCgSFjJKc14kEg==",
          "place_id": "NzYqAQYCRxsDWl5TDSlXFgABEEJ+dThAMBsPcFkGSCgSFjJKc14kEg==",
          "name": "明美藥局",
          "city": "台北市",
          "town": "中山區",
          "type": "地點",
          "cat": "藥局",
          "distance": 6.211
        }
      ]
    }
    ```

- [back to index](#api-index)

----

<p align="center">
<a href="https://map8.zone"><img src="../../images/logo_96x96.png" /></a> <br/> https://map8.zone
</p>
