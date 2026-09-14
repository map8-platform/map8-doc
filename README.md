# 台灣圖霸電子地圖 API 平台 | Map8 Platform
歡迎使用 **<img src="images/logo.png" width="28" height="28"> 台灣圖霸電子地圖 API 平台 | Map8 Platform**

- 歡迎試用我們的 **台灣圖霸電子地圖 API 平台 (Map8 Platform) API**!! [請點此申請試用](https://docs.google.com/forms/d/1BMN0cnmROBvtfU1JAxk-2sR9KcZdViHMNFtsyTR12l8)~
- 然後也歡迎您到我們的 [官方 API Explorer](https://www.map8.zone/api-explorer) 試用看看~ :smile:
- 完整線上版 API 文件請見 : https://www.map8.zone/map8-api-docs/

有任何技術疑難，歡迎您發到 [issues 專區](/../../issues)~

或是有其他任何疑問，也都歡迎您 [跟我們聯絡](https://www.map8.zone/contact) 喔!!!

<br/>

## Authentication (認證與授權)
**台灣圖霸電子地圖 API 平台 | Map8 Platform** 的 API 透過 `API key` 來讓您使用平台的 API。您可以 [點此申請試用](https://docs.google.com/forms/d/1BMN0cnmROBvtfU1JAxk-2sR9KcZdViHMNFtsyTR12l8)。

**台灣圖霸電子地圖 API 平台 | Map8 Platform** 的 API 預期來自 client 端的所有 API 請求於 URL 的 query string 中以 `key` 參數夾帶 API key 以認證並取得使用，例如 :

```
https://api.map8.zone/v2/place/geocode?address=台北市內湖區港墘路200號&key=<您的 key>
```

> 請務必將上例 `<您的 key>` 替換成您的 `API key` 喔!!!

## Notation
1. 左右鍵符號 (大於、小於符號, 也就是 `<` `>`) 所描述的是一個變數 (variable) 的 formal parameter 形式。本文件底下若提及參數部分，使用到此表示法時，請讀者將之代換成實際的內容 (也就是代換為 actual parameter，並且，不留下 `<` `>` 符號)。例如, `https://api.map8.zone/find?keyword=<關鍵詞>` 若實際關鍵詞為 taiwan，則實際呼叫時，應代換為 `https://api.map8.zone/find?keyword=taiwan`。
2. 位於 **API** 欄位內格式的 `<參數>`，為標準的 URL 之 query string 格式編碼 (i.e., `name=value` 以 URL `%` 編碼, 並以 `&` 連接)
3. 除非另有指定，否則，地理經緯度座標 (lat 或 latitude 均指緯度，lng 或 longitude 均指經度) 以 WGS84 / EPSG:4326 為地理座標系統

> 請注意 : 本文件所述 `true` / `false` 如字面 (string literal)，也就是以 `true` (四個字元) 或 `false` (五個字元) 帶入 (而非以 1 / 0 或其它字元帶入)。

> 提醒您 : URL 所有以 [query string](https://en.wikipedia.org/wiki/Query_string) 形式帶入的參數，應當都要視情況，適當地經過 [URL encode](https://en.wikipedia.org/wiki/Percent-encoding) 編碼後再傳入。以免發生錯誤而收到 HTTP 400 Bad Request。

> 請留意 : URL 必須正確編碼，並且所有 Map8 API 均限制在最多 8192 個字元。

## Version
- v3.1_2025-09-19 (the present document)
    1. [Address Standardization API](./api/address-validation/address-standardization-api.md) 增加地址中翻英功能 (formatted_address_en ...等英文欄位)。
- [v3.0_2022-10-04](https://www.map8.zone/map8-api-docs/map8-api-docs_v3.0.17_2022-10-04_1e18352c/)
    1. [Snap to Roads API](./api/roads/snap-to-roads-api.md) 增加回應道路所屬行政區資訊 (city / town 欄位)。
- [v3.0_2020-10-13](https://www.map8.zone/map8-api-docs/map8-api-docs_v3.0.15_2020-10-13_53954a8a/)
    1. [Nearby Search API](./api/places/places-api.md#nearby-search-api) 單次回應筆數提高至最多可為 100 筆。
- [v3.0_2020-01-06](https://www.map8.zone/map8-api-docs/map8-api-docs_v3.0.8_2020-06-02_22e8f5fe/)
    1. Roads 新增 [Snap to Roads API](./api/roads/snap-to-roads-api.md)。
    2. Routes 新增 [Distance Matrix API](./api/routes/distance-matrix-api.md)。
    3. Routes 新增 [Trip API](./api/routes/trip-api.md)。
- [v2.1_2019-07-29](https://www.map8.zone/map8-api-docs/map8-api-docs_v2.3_2019-11-14_8d0e8ff9/)
    1. 大幅強化 [Roads API](./api/roads/nearest-roads-api.md) 的性能 (準確度)。請您使用新版本，以取得最新性能。
        - URL 由 https://api.map8.zone/route/road 進版為 https://api.map8.zone/`v2`/route/road/<交通工具>。
        - 新增進階參數包括 `bearing`, `range`, `radius` (行進角度與容許誤差，以及地理方圓範圍)。讓您藉由自 GPS 定位裝置所取得之額外定位資訊，獲取更精準的道路定位結果。
- [v2.0_2019-06-26](https://www.map8.zone/map8-api-docs/map8-api-docs_v2.0_2019-06-26_95b060ca/)
    1. 請注意 : 本次更版，下列 API 版號有所進版，因而 URL 路徑也有變更。請您使用新版本，以取得最新性能。
        1. [Places API](./api/places/places-api.md) 全部，包括 :
            1. Find Place API 由 https://api.map8.zone/place/findplacefromtext 進版為 https://api.map8.zone/`v2`/place/findplacefromtext。
            2. Nearby Search API 由 https://api.map8.zone/place/nearbysearch 進版為 https://api.map8.zone/`v2`/place/nearbysearch。
            3. Place Autocomplete API 由 https://api.map8.zone/place/autocomplete 進版為 https://api.map8.zone/`v2`/place/autocomplete。
    2. 新增 [Place Details API](./api/places/places-api.md#place-details-api)。
    3. 新增 `postcode` 與 `formatted_address_embed_postcode` 參數，以提供您地址之郵遞區號。
    4. 請注意 : 本版本起，[Place Autocomplete API](./api/places/places-api.md#place-autocomplete-api) 用法有所改變 -- 如同 Google 用法，Place Autocomplete API 不提供座標。請於使用者選定 autocomplete 所提供之候選清單之一的時候，透過 Place Details API 取得地點的詳細資訊。
    5. Nearby Search API 與 Text Search API 新增 `cat` 參數，支援指定欲搜尋的 [地點類型](./api/appendix.md#地點類型)。
    6. Map8 台灣圖霸電子地圖 API 平台維護了最具權威性的門牌地址資料庫，並隨時與台灣戶政司門牌地址資料保持更新。[Geocoding API](./api/places/geocoding-api.md) 新增提供 `level` (定位結果的層級，譬如，為定位到縣市、或是道路，或是精確到門牌)。或者門牌並不存在，因而透過內插 (interpolation)，或是改經由模糊搜尋並給予可能性指數 `likelihood`。並透過 `authoritative` 欄位告訴您定位結果是否出自於 Map8 台灣圖霸電子地圖 API 平台最具權威的門牌地址資料庫。
- 舊版
    - (不再支援) [v1.1_2019-05-26](https://www.map8.zone/map8-api-docs/map8-api-docs_v1.1_2019-05-26_301e4b5a/)

> 如果您在找的是台灣圖霸電子地圖 API 平台 Map8 「尊爵版 API」: 最適合房仲業不動產物件的 `生活機能` 與 `嫌惡設施` API，請 [聯繫](https://www.map8.zone/contact) 我們為您服務。

<br/>

## API Index

## Maps 類
1. **[Maps Embed API](./api/maps/maps-embed-api.md)**
    - 不需要任何程式碼，用最簡單的方式 (網址傳遞參數)，就可以在您的網站嵌入動態的互動式地圖
2. **[Maps Static API](./api/maps/maps-static-api.md)**
    - 製作顯示地圖的圖檔，讓您在網站或其他任何素材中嵌入靜態地圖
    - [Map8 Platform API Explorer](https://www.map8.zone/api-explorer/#/%5BMaps%5D%20Static%20API)
3. **[Maps Javascript API](https://www.map8.zone/vector/index)**
    - 為您的網站添加互動式地圖。擁有自己的地圖內容與圖樣

## Places 類
提供全台灣超過 **650 萬筆** `門牌地址`、**100 萬筆** `道路`、**300 萬筆** `景點 POI` 之豐富圖資的搜尋功能。

每月圖資更新，每天路調，專業製圖，品質極佳 (不似有些坊間電子地圖，景點雖多，但品質良莠不齊，令人擔心)。

一次的 response 即將全部欄位資料送回給您。讓您的工程師 coding 起來輕鬆，維護起來簡單，更新功能快速，更棒的是，您不必擔心被剝好幾次皮。

極佳的運算能力，瞬間即回應您的 request，滿足您對 QoS (服務品質) 的嚴格要求。

此外，跟 Google Maps 不一樣的地方是，我們直接在結果內回傳距離給您!!!

Places 提供底下 API's :

1. **[Places API](./api/places/places-api.md)**
    - **Place Search** : 搜尋地圖圖資, 包括 :
        - **[Find Place API](./api/places/places-api.md#find-place-api)**
            - 給定關鍵詞，搜尋地點
        - **[Place Details API](./api/places/places-api.md#place-details-api)**
            - 取得地點詳細資訊 (主要用於 Autocomplete API)
        - **[Nearby Search API](./api/places/places-api.md#nearby-search-api)**
            - 給定座標，搜尋周遭
        - **[Text Search API](./api/places/places-api.md#text-search-api)**
            - 以任意的關鍵詞組合進行搜尋 (請把這個當作搜尋引擎, 關鍵字以空白分隔, 例如 "加油站 台中" 這樣即可得到滿意的結果)
    - **[Place Autocomplete API](./api/places/places-api.md#place-autocomplete-api)**
        - 讓使用者邊輸入，便邊回應出推測的可能清單 (通常運用在需要極佳使用體驗, 逐字逼近搜尋目標物的場景上)
    - [Map8 Platform API Explorer](https://www.map8.zone/api-explorer/#/%5BPlaces%5D%20Find%20Place%20API)

2. **[Geocoding API](./api/places/geocoding-api.md)**
    - [地址定位](./api/places/geocoding-api.md#geocoding-api) : geocoding, 也就是將 `地址 / 門牌` 轉為地理座標 `經緯度`
    - [反地址定位](./api/places/geocoding-api.md#geocoding-api) : reverse geocoding, 也就是將地理座標 `經緯度` 轉為 `地址 / 門牌`
    - [Map8 Platform API Explorer](https://www.map8.zone/api-explorer/#/%5BPlaces%5D%20Geocoding%20API)

3. **Places Library, Maps Javascript API**
    - 為您的網站添加互動式地圖，並加入台灣圖霸電子地圖 API 平台的圖資搜尋功能 (以 javascript library 的形式提供您簡單的開發應用介面)

## Routes 類
利用 Map8 圖資所計算出之路網資訊, 提供多點路徑規劃、多點旅行之距離時間矩陣

1. **[Directions API](./api/routes/directions-api.md)**
    - 依所給定之起訖點 (與數個中途點) 順序，進行多點路徑規劃
2. **[Distance Matrix API](./api/routes/distance-matrix-api.md)**
    - (排班) 運算多對多起訖點之交通時間距離矩陣
3. **[Trip API](./api/routes/trip-api.md)**
    - (排行程) 給定多途經點，排出次序並規劃路徑 (旅行業務員 / TSP 最短路徑問題)

## Roads 類
取得道路屬性與黏路

1. **[Nearest Roads API](./api/roads/nearest-roads-api.md)**
    - 取得道路屬性 (譬如道路速限、高架、橋樑、限高)
2. **[Snap to Roads API](./api/roads/snap-to-roads-api.md)**
    - 對地圖黏岀最可能路徑 (黏路)

## Address Validation 類
地址正規化 / 地址校正 / 地址勘誤

1. **[Address Standardization API](./api/address-validation/address-standardization-api.md)**
    - 地址正規化 / 地址校正 / 地址勘誤，並可回傳英譯地址

## 附錄
- [HTTP Status Code](./api/appendix.md#http-status-code)
- ["status" 欄位](./api/appendix.md#status-欄位)
- [地點類型](./api/appendix.md#地點類型)

<br/>

## 優點
- 輕量化的向量圖磚，檔案小，傳輸速度快
- `嵌入了圖資` 內，使用者體驗會感覺與底圖是 `一體` 的 (而不若傳統透過 GeoJSON / KML 感覺與底圖是分開的，而且難以支撐大規模資料)
- `高度互動` (interactive)、 `可共享` 的 `視覺化` (sharable visualizations) 分析呈現
- `任選區域` 檢視，zoom-in，獲得 `更高解析度` 而且 `不失真`
- 資料視覺化 (data visualization)、高度互動 (highly interactive)、可共享 (shareable) 的 solution

    > 現代 BI (Business Intelligence) 的需求，是遠遠超過於傳統分析報表僅止於圖 (graphs) 與表 (charts) 的。您想要鑑別哪些投保物件恰位於風暴路徑上嗎? 或是想比對建置成本與人行流量以最佳化展店地點嗎? 想規劃運送路徑以避開交通並降低油耗?
    - `台灣圖霸電子地圖 API 平台` 正是這些問題的最佳解答。而且，視覺化 (visual) 且互動 (interactive)

#### `台灣圖霸電子地圖 API 平台` https://map8.zone 提供您基於地圖的資料分析視覺化、高度互動、可共用的極佳使用體驗!!!

<br/>

## 特色
### 地圖資料豐富
資料庫內含 PAPAGO! 導航所使用之地圖資料，包含導航路網，興趣點及門牌店家。 透過 API 展示、應用各項資訊。

### 極佳相容性
支援使用者添加自有圖片或其他網路地圖服務，亦可透過藉由資料格式支援、提供資料繪製、輸出等方法讓資訊呈現更加多元。

### 功能強大
提供坐標轉換、量測、定位、查詢、路徑規劃、使用者位置等功能，讓各種應用開發更加便利。

### 支援向量圖磚
資料量小，大幅減少資料更新的時間。可動態改變地圖外觀、疊加 3D 建物。任意視角、360 度旋轉、縮放平移更加流暢不失真。
<br/><sub>* 與網格圖磚相比</sub>

### 客製服務
可為您路調，製作屬於您的特定區域圖資。除了為您量身訂製專有圖層或是套疊您的自有圖層，更可自由決定全受管或是自行管理服務主機，一切隨您！自有圖資自己管，維護在私有網路內，自有圖資與數據資安更有保障。

### 彈性方案
可選擇按月、按季度、按年的租賃方案或依據使用流量進行計費。

<br/><br/>

----

<p align="center">
<a href="https://map8.zone"><img src="images/logo_96x96.png" /></a> <br/> https://map8.zone
</p>
