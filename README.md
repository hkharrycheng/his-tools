# HIS Tools

Harry's Intelligent Services - 一個 Mobile Friendly 嘅工具集合 Landing Page。

---

## 📋 目錄

1. [ADMIN 使用說明](#admin-使用說明)
2. [Logic Flow](#logic-flow)
3. [Business Flow](#business-flow)
4. [JSON Structure](#json-structure)
5. [文件結構](#文件結構)
6. [Version Control](#version-control)
7. [常見問題](#常見問題)

---

## ADMIN 使用說明

### 前置條件

- GitHub Account（用於存放程式同 GitHub Pages 託管）
- 本地 OneDrive 目錄：`C:\Users\Harry\OneDrive\Harry Information System\豆包Agents Program Backup\Main\`

### 初始設定

1. 在本地 OneDrive 目錄中修改 `index.html`
2. 在瀏覽器中打開本地文件測試
3. 確認滿意後，呼叫助手執行 J02M
4. 助手會自動上傳到 GitHub 同備份到雲端

### 日常操作

#### 更新 Landing Page

1. 在本地 OneDrive 目錄修改 `index.html`
2. 本地測試
3. 向助手提出更新要求
4. 助手建立 Prototype 讓你測試
5. 你答「OK」後，助手會：
   - 上傳到 GitHub
   - 更新 README.md
   - 備份整個 Repository 到雲端

#### 新增工具

1. 在飛書多維表格「Register List」中新增工具記錄
2. 填寫 HIS Tools（工具名稱）、Deployed Page、圖標、描述、Tags 等欄位
3. 呼叫 J00 生成 tools.json 並上傳到 GitHub
4. HTML 會自動從 tools.json 動態載入工具列表
5. 工具按 Tags 分組顯示，用戶可 toggle 顯示/隱藏

---

## Logic Flow

### 頁面載入流程

```
用戶打開 index.html
        │
        ▼
  updateDate()
        │
        ├─ 顯示當前日期（MM/DD）
        └─ 顯示星期
        │
        ▼
  loadWeather()
        │
        ├─ 呼叫 Open-Meteo API（香港坐標）
        ├─ 獲取溫度同天氣代碼
        └─ 顯示天氣圖標 + 溫度
        │
        ▼
  loadTools()
        │
        ├─ 檢查協議：file:// → 用內嵌 DEFAULT_TOOLS
        └─ http/https → fetch tools.json
        │
        ▼
  initTagsAndRender()
        │
        ├─ 提取所有 Tags（交通、購物、社交等）
        ├─ 預設所有 Tag 顯示
        ├─ 渲染 Tag 篩選按鈕
        └─ 按 Tag 分組渲染工具卡片
        │
        ▼
  頁面就緒
        │
        ├─ 點擊日期 → 打開 Google Calendar
        ├─ 點擊天氣 → 打開香港天文台
        ├─ 點擊 Tag 按鈕 → toggle 顯示/隱藏分組
        ├─ 點擊工具卡片 → 打開對應工具
        └─ 輸入搜尋 → 即時過濾工具卡片
```

### Tags 分組流程

```
用戶點擊 Tag 按鈕
        │
        ▼
  toggleTag(tag)
        │
        ├─ Tag 已顯示 → 從 activeTags 移除
        └─ Tag 已隱藏 → 加入 activeTags
        │
        ▼
  updateTagButtons() → 更新按鈕樣式
        │
        ▼
  renderTools()
        │
        ├─ 遍歷所有工具
        ├─ 檢查工具的 Tags 是否在 activeTags 中
        ├─ 按 Tag 分組顯示
        └─ 更新每組數量
```

### 搜尋功能流程

```
用戶輸入搜尋字
        │
        ▼
  遍歷所有 .tool-card
        │
        ├─ 比對 data-name（工具名稱）
        ├─ 比對 data-desc（工具描述）
        │
        ▼
  顯示/隱藏卡片
        │
        ├─ 有結果 → 更新顯示數量
        └─ 冇結果 → 顯示「搵唔到相關工具」
```

---

## Business Flow

### J02M：HIS Main Page 更新與備份

```
觸發：用戶提出更新要求 或 直接呼叫備份
        │
        ▼
  情況 A：有更新要求
        │
        ├─ 1. 建立本地 Prototype
        │   └─ 修改 OneDrive 目錄中的 index.html → 本地測試
        │
        ├─ 2. 用戶測試並確認
        │   ├─ 用戶答「OK」→ 繼續
        │   └─ 用戶要求修改 → 回到步驟 1
        │
        ├─ 3. 更新 GitHub
        │   ├─ 上傳修改後嘅 index.html
        │   ├─ 上傳相關資源（圖片等）
        │   └─ 更新 README.md
        │
        └─ 4. 備份 Repository → 跳到情況 B
        │
  情況 B：直接備份
        │
        ├─ 1. 下載 GitHub Repo ZIP
        │
        ├─ 2. 解壓 ZIP
        │
        ├─ 3. 上傳到飛書雲端
        │   ├─ 按照 GitHub 文件夾層級創建文件夾
        │   └─ 逐個上傳文件（.html, .md, 圖片等）
        │
        └─ 4. 驗證備份完成
        │
        ▼
  完成 ✅
```

### 工作規則：上載 Repository 前，>500 KB 的檔案一律先壓縮（2026-11-09 定案）

- **觸發**：任何把檔案（尤其 `images/` 內的圖）上載到 GitHub repo／Pages 或 Cloudflare Pages 之前。
- **規則**：單檔 **> 500 KB** 必須先壓縮到 **< 500 KB** 才上載。JPEG 逐級降 quality（88→50，`optimize`＋progressive）；PNG 轉 256→64 色調色板（必要時先降 scale）；**保持原檔名副檔名**，令前端 token 路徑不變。
- **例外：GIF 不壓**。重壓 GIF 實測會令檔案變大（`shrink_gifs.py` 已廢棄），現有 5 個 >500 KB 的 GIF 保留原檔。
- **理由**：GitHub 每個 repo 的 Pages 容量上限 1,024 MB、Cloudflare Pages 每 deployment 20,000 檔；壓縮是唯一不動引用關係而能省容量的手段。J16 HKB `images/` 由 725.7 MB 壓到 460.5 MB（326 個檔案）即按此規則。
- **驗收**：壓縮後抽 2 張最大圖目視核對文字仍清晰，並回讀 repo 的 blob 大小確認 < 500 KB 或屬 GIF 例外。
- **不做的事**：Base 多維表格內的附件**保留原圖**（用戶 2026-11-09 決定），壓縮版只用在 repository／部署端。

---

## JSON Structure

### tools.json

工具列表由飛書多維表格「Register List」生成，透過 J00 上傳到 GitHub。

```json
[
  {
    "name": "巴士到站時間",
    "icon": "🚌",
    "url": "https://hkharrycheng.github.io/bus-eta-sync/bus-eta.html",
    "description": "九巴、城巴/新巴、小巴實時到站時間，常用路線一覽",
    "status": "已上線",
    "tags": ["交通"]
  },
  {
    "name": "外幣夾錢",
    "icon": "💱",
    "url": "https://hkharrycheng.github.io/currency-exchange/",
    "description": "實時外幣兌換率查詢 + 夾錢計算",
    "status": "已上線",
    "tags": ["購物"]
  }
]
```

### 欄位說明

| 欄位 | 類型 | 說明 |
|------|------|------|
| name | string | 工具名稱（嚴格跟 Register List 的 HIS Tools 欄） |
| tags | array | 標籤列表，嚴格跟 Register List 的 Tags 欄（現有：交通／戶外／學業／電玩／家庭／資料庫） |
| github | string | GitHub Repository 連結（無 repo 時留空） |
| icon | string | emoji 圖標（卡片未用 logo 時的後備） |
| description | string | 工具描述（嚴格跟 Register List 的「描述」欄逐字） |
| deployedPage | string | 工具部署頁面連結（跟 Register List 的 Deployed Page） |
| status | string | 狀態（已上線／即將推出），跟 Register List 的「狀態」欄 |
| baseUrl | string | 對應飛書／豆包 Base 連結 |
| logo | string | 卡片用圖標圖檔（repo 內路徑，例 `logos/mho.jpg`；用 Register List「圖標」欄原圖、不改 bytes） |

---

## 文件結構

### GitHub Repository

```
his-tools/
├── index.html              # Landing Page 主頁面
├── tools.json              # 工具列表（由 Register List 生成）
├── citybus-logo.png        # 城巴 Logo
└── README.md               # 本文件
```

### 本地 OneDrive 目錄

```
C:\Users\Harry\OneDrive\Harry Information System\豆包Agents Program Backup\Main\
├── index.html              # Landing Page 主頁面（本地開發版）
├── tools.json              # 工具列表
└── citybus-logo.png        # 城巴 Logo
```

### 飛書雲端備份

```
HIS Tools Backup/ (RJVkfrQ6fl9RDCdDgX0cEflKn2g)
├── index.html
├── citybus-logo.png
├── README.md
└── his-tools-backup-YYYYMMDD-HHMMSS.zip
```

---

## 系統現狀（2026-11-09）

### Register List 新增兩條使用率欄

`Register List`（table `tblOFf8q07WDTxNK`）除「使用容量 (MB)」「檔案數」外，另設兩條互斥的使用率欄：

| 欄位 | 分母 | 口徑 |
|------|------|------|
| GitHub 使用率 | 1,024 MB | 只有 HTML 真係由該 GitHub repo 的 Pages 託管才計；唔係就填 `N/A` |
| Cloudflare 使用率 | 20,000 檔 | 只有 HTML 真係部署在該 Cloudflare Pages 專案才計；唔係就填 `N/A` |

兩欄都係 formula，回讀係**小數**（平台會把 `&"%"` 改寫成 `/100`）；**小數係預期行為，不需要修正**（例：家庭日記回讀 `0.6827` ＝ 68.27%）。GitHub Pages 的 1 GB 係 **per repository**（每個 repo 的 Pages 站各自計），唔係 per account。

### 各工具託管與規模

| 工具 | 託管 | 檔案數 | 圖片數 | 容量 (MB) |
|------|------|--------|--------|----------|
| 家庭日記 J12 | Cloudflare Pages `memories-blogger` | 13,653 | 13,650 | 1,295.2 |
| HKB Reader J16 | GitHub Pages `hkb-reader` | 3,336 | 3,224 | 460.5（壓縮後） |
| 家庭醫療 J13 | Cloudflare Pages `family-health-241` | 13 | 7 | 2.7 |
| 音樂庫 | Cloudflare Pages `music-inventory` | 13 | 0 | 6.5 |
| 其餘（巴士／外幣／點餐／貨品／學生／Wild Rift／鳴潮／國泰／升中／影片／兒童遊戲／MHO 等） | GitHub Pages | 見 Register List | 見 Register List | 見 Register List |

J12 2026.1.19（2026-11-09）重點：① 圖片引用覆蓋率 100%（13,650/13,650）、線上孤兒 **0**；② **CI 邏輯改為「manifest 自動修剪」**——`main()` 以 `json_refs()`（JSON 全部被引用的 `images[].t` ＋ people／buddies 的 `pict`）作唯一權威，不再 union 舊 manifest／CF 已部署 token；`download_imgs()` 只下載 manifest 內缺檔的 token（原本 `t not in manifest` 會把未引用圖塞回 manifest）；`deploy()` 加「有 manifest 但本機缺圖即中止」守門。孤兒由 496 歸 0，部署 13,653 檔／13,650 圖、`manifest.json` 423,150 bytes（13,650 token）、名額 **68.3%**。③ 修好 `deploy()` 的 `>2 KB` 尺寸門檻（改 `>0`），7 張 320B–1.2KB 細圖不再被跳過（長期「缺 6 張」根因）。④ 追查「版本號改極都唔生效」：`scripts/j12_table_patch.py` 內 `re.sub(..., '2026.1.18')` 每次 CI 都把主腳本版本硬改回舊號，已改 `2026.1.19`（commit `291a1f71`）；最終 production **`579033b6`**（13,653／13,650／孤兒 0，`family-diary.json` version 2026.1.19），manifest 與引用集合雙向差集 0／0。

J16 HKB Reader 2026.1.08（2026-11-09）重點：**學科名換新（EDUC）＋舊名歸位**——`build_hkb.py` 加 `norm_doc()` 名稱正規化（統一空白、去掉檔名式 ` - `），顯示名改用磁碟 DOCX 檔名，`EDUC10 Practical English`／`EDUC11 Business Email` 歸回 `Philosophy and Anthropology`，`EE01／MAT01` 統一顯示為 `EE01 - Electronic Engineering`／`MAT01 - Material Engineering`；**未分類 37 單元／96 篇章歸零**，10 個 STREAM 全部有單元。資料層重抓四表（675 單元／792 篇章／372 細節／97 Links），`hkb.json` 2,078,594 bytes、已推 `hkb-reader` repo（commit `4b32ee57`）並建 `versions/v2026.1.08/` snapshot。

J10 升中分析 v1.02（2026-11-11）重點：**地圖改 Leaflet**（`leaflet/` 本地打包、Esri Light Gray 免 key 底圖），只以圓點顏色表示 Band、**區域不再填色**；新增「留意名單」圖庫 TAB，卡內「去地圖定位」可切回地圖 `flyTo` zoom 16；圓點互動改為**先出學校詳情卡 → 右下角按鈕才 toggle 留意名單 → 二次確認 → 寫多維表格 → 重生 j10.json 回傳本頁**，TAB1 預設只看留意名單；修好四個靜默問題：① overlay（確認框／詳情卡／toast／底部 Tab）z-index 低於 Leaflet pane 被地圖蓋住；② 瓦片 opacity 卡 0 令地圖空白（`fadeAnimation:false`）；③ canvas 圓點在尺寸變更後偏移／消失（改 SVG renderer ＋ resize 重畫）；④ CDN 掛掉令全頁 JS 唔執行（`L` undefined、地圖空白但 DOM 正常）→ Leaflet 本地打包。`j10.json` 151 間／留意名單 4／Band 1 共 100（130,389 bytes）；commit `c212c3c012`，`versions/v1.02/` 已建。

---

## Version Control
> 版本號口徑（2026-11-08 統一）：README、HTML footer、tools.json 版本記錄全部用同一號制 `2026.1.XX`，與 HTML 最底 `HIS Tools © 2026.1.XX` 完全對齊；舊記錄的 `v1.0X` 即等同 `2026.1.0X`（例如 v1.09 = 2026.1.09），已在本節統一改寫。
- 2026.1.12 (2026-11-14): J13 家庭醫療記錄卡片圖標改透明 cutout（`logos/health.png` 1.87MB→132KB，BB 相去白底；J01 只對該卡加 `.health-icon` 透明底，其他卡不變）；GitHub Pages 已 built、線上驗證
- 2026.1.10 (2026-11-08): 加入「魔物獵人 旅人助手」J17 工具卡片（GitHub Pages：hkharrycheng.github.io/mho-helper/，Tags 電玩，logo `logos/mho.jpg` 用 Register List 圖標原圖）；tools.json 增至 17 條；HTML footer、README、版本記錄三者對齊 2026.1.10
- 2026.1.09 (2026-10-30): 加入「家庭醫療記錄」工具卡片（Cloudflare Pages：family-health-241.pages.dev）；J13 2026.1.06 上線
- 2026.1.08 (2026-10-19): 加入「家庭日記」工具卡片（Cloudflare Pages：memories-blogger.pages.dev + Turnstile 公開閘）；tools.json 重新生成
- 2026.1.07 (2026-10-13): 統一圖標 LOGO（tools.json logo 字段 + Main 卡片顯示圖標相片）

| 版本 | 日期 | 更新內容 |
|------|------|----------|
| 2026.1.06 | 2026-10-12 | 國泰機票助手卡片改用 Cathay 官方 LOGO（cathay-logo.png）取代 ✈️ emoji |
| 2026.1.05 | 2026-10-12 | 加入「鳴潮助手」同「國泰機票助手」工具卡片；tools.json 重新生成（補回鳴潮 + 新增國泰） |
| 2026.1.04 | 2026-10-12 | 加入 notranslate 禁止瀏覽器翻譯；修正 tools.json 格式為純數組；統一 J00/J01 格式 |
| 2026.1.03 | 2026-10-11 | 簡化為一個「📊 多維表格」按鈕（JavaScript智能跳轉：先試飛書APP，2秒冇反應自動轉網頁版）；J06改用新「AI補習社」多維表格 |

### 版本規則

- 初始版本：2026.1.00
- 每次更新 +0.01
- HTML 最底必須顯示：`HIS Tools © 2026.1.XX`（XX 為版本號小數位）
- README.md 必須記錄每次更新日期同改動內容

### 更新記錄

| 日期 | 版本 | 說明 |
|------|------|------|
| 2026-11-12 | 2026.1.10（不變） | J12 家庭日記啟用 Cloudflare Access 登入保護：主網域 `memories-blogger.pages.dev` 及預覽 `*.memories-blogger.pages.dev` 未登入均 302 去 Access；改用 **Cloudflare account 登入**（非 email OTP），IdP `b43182fc`、`restrict_to_account_members=false`，policy 允許 `hkchengtszkin@yahoo.com.hk`、`hkharrycheng@gmail.com`；Access app `d5617021`／預覽 `c9ea3190`；Pages worker Turnstile 仍保留（登入後再驗一次） |
| 2026-11-12 | 2026.1.10（不變） | J09 國泰機票及旅遊助手 v1.24：手機天數進度改 4 欄卡片格（8 天 4×2、14 天 4×4），只須上下滾動、不再橫向溢出；首頁版本升 v1.24、右上角時間跟隨最新 JSON；index commit 7d8fb96 |
| 2026-11-12 | 2026.1.10（不變） | J09 國泰機票及旅遊助手 v1.23：修復 Timeline 破圖（分批載入＋自動重試 2 次）、航班票價面板改橫向比例條、統計圖表移除 fadeIn 並只在可見時初始化；同步修復 CI 重生 cathay.json 遺失 topFlyers／乘客關聯（單條 link dict 回傳），topFlyers 8／19 張航班有乘客名；tools.json、HTML 未改，版本號維持 |
| 2026-11-09 | 2026.1.10（不變） | README 補「系統現狀」：Register List 新增 GitHub ／ Cloudflare 兩條使用率欄口徑；J12 家庭日記圖片覆蓋 100%（13,650 張）、**CI 改為 manifest 自動修剪（孤兒 496→0，2026.1.19）**；HKB Reader 壓縮後 3,224 張／460.5 MB，**2026.1.08 學科換新（EDUC）＋舊名歸位（未分類歸零）**。HTML、tools.json 未改，故版本號維持 2026.1.10 |
| 2026-11-08 | 2026.1.10 | 加入「魔物獵人 旅人助手」J17 卡片（GitHub Pages：hkharrycheng.github.io/mho-helper/）；tools.json 17 條，Tags 電玩，logo 用 Register List 圖標原件（logos/mho.jpg） |
| 2026-10-30 | 2026.1.09 | 加入「家庭醫療記錄」J13 工具卡片（Cloudflare Pages：family-health-241.pages.dev）；J13 2026.1.06 修正所有指標 L/H 參考線對齊 Family Health Base |
| 2026-10-19 | 2026.1.08 | 加入「家庭日記」工具卡片（Cloudflare Pages 部署 + Turnstile 公開閘防爬）；tools.json 重新生成 |
| 2026-10-12 | 2026.1.06 | 國泰機票助手卡片改用 Cathay LOGO（cathay-logo.png）；升版本至 2026.1.06 |
| 2026-10-12 | 2026.1.05 | 加入鳴潮助手⚔️同國泰機票助手✈️到 tools.json；升版本至 2026.1.05 |
| 2026-10-12 | 2026.1.04 | 加入 notranslate 禁止瀏覽器翻譯；tools.json 改為純數組 |
| 2026-10-11 | 2026.1.02 | 移除「其他」TAG，嚴格跟Register List；工具卡片加入「📊 多維表格」飛書APP deeplink鏈接；加入遊戲🎮 TAG圖標；強化工作流程（先改OneDrive→localhost測試→用戶OK→上傳GitHub） |
| 2026-10-09 | 2026.1.01 | 加入 Tags 分組顯示同 toggle 功能；工具名稱嚴格跟 Register List；動態讀取 tools.json；加入內嵌預設數據支援本地 file:// 測試 |
| 2026-10-08 | 2026.1.00 | 初始版本，Mobile Friendly Landing Page，包含巴士到站查詢工具；Calendar 改用 Mobile Deeplink |

---

## 常見問題

### Q: 點解天氣顯示唔到？

A: 可能原因：
1. 網絡問題 → 檢查網絡連接
2. Open-Meteo API 暫時不可用 → 稍後重試
3. 瀏覽器阻止跨域請求 → 檢查瀏覽器設定

### Q: 點樣新增工具？

A: 
1. 在飛書多維表格「Register List」中新增工具記錄
2. 填寫 HIS Tools、Deployed Page、圖標、描述、Tags 等欄位
3. 呼叫 J00 生成 tools.json 並上傳到 GitHub
4. HTML 會自動從 tools.json 動態載入工具列表

### Q: Tags 分組點樣運作？

A: 每個工具可以設定一個或多個 Tags（交通、購物、社交等）。頁面會按 Tags 分組顯示，用戶可以點擊 Tag 按鈕來 toggle 顯示/隱藏該分組。「全部」按鈕可以一鍵顯示/隱藏所有分組。

### Q: 點解本地打開 index.html 都用到？

A: 因為 HTML 內嵌咗 DEFAULT_TOOLS 預設數據。當檢測到 file:// 協議時，會直接用內嵌數據，唔使 fetch tools.json。部署到 GitHub Pages 後就會自動改用 tools.json。

### Q: 可以自訂天氣位置嗎？

A: 可以。修改 `loadWeather()` 函數中嘅 `lat` 同 `lon` 變數就得。目前設定為香港（22.3193, 114.1694）。

### Q: 點解日期係 MM/DD 格式？

A: 為咗節省 Header 空間，用咗簡潔嘅 MM/DD 格式。點擊日期會直接打開你手機嘅 Calendar App（iOS 用 calshow://，Android 優先 googlecalendar://）。

### Q: Calendar Deeplink 點樣運作？

A: `openCalendar()` 函數會檢測用戶設備：
- **iOS**: 用 `calshow://` deeplink 直接打開系統日曆 App
- **Android**: 優先嘗試 `googlecalendar://`，失敗則打開網頁版 Google Calendar
- **桌面**: 打開網頁版 Google Calendar

### Q: 搜尋支援邊啲語言？

A: 支援中英文搜尋。比對工具名稱同描述，大小寫不敏感。

---

## 外部連結

- **Landing Page**: https://hkharrycheng.github.io/his-tools/
- **家庭醫療記錄 J13**: https://family-health-241.pages.dev
- **家庭日記 J12**: https://memories-blogger.pages.dev
- **巴士到站查詢**: https://hkharrycheng.github.io/bus-eta-sync/bus-eta.html
- **香港天文台**: https://www.hko.gov.hk/
- **Google Calendar**: https://calendar.google.com/

---

## License

MIT License
