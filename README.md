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
| icon | string | 工具圖標（emoji 或圖片 URL） |
| url | string | 工具部署頁面連結 |
| description | string | 工具描述 |
| status | string | 狀態（已上線/即將推出/開發中） |
| tags | array | 標籤列表，用於分組顯示（交通/購物/社交/其他） |

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

## Version Control

### 版本規則

- 初始版本：v1.00
- 每次更新 +0.01
- HTML 最底必須顯示：`HIS Tools © 2026.1.XX`（XX 為版本號小數位）
- README.md 必須記錄每次更新日期同改動內容

### 更新記錄

| 日期 | 版本 | 說明 |
|------|------|------|
| 2026-10-09 | v1.01 | 加入 Tags 分組顯示同 toggle 功能；工具名稱嚴格跟 Register List；動態讀取 tools.json；加入內嵌預設數據支援本地 file:// 測試 |
| 2026-10-08 | v1.00 | 初始版本，Mobile Friendly Landing Page，包含巴士到站查詢工具；Calendar 改用 Mobile Deeplink |

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
- **巴士到站查詢**: https://hkharrycheng.github.io/bus-eta-sync/bus-eta.html
- **香港天文台**: https://www.hko.gov.hk/
- **Google Calendar**: https://calendar.google.com/

---

## 更新記錄

| 日期 | 版本 | 說明 |
|------|------|------|
| 2026-10-08 | v1.1 | Calendar 改用 Mobile Deeplink，iOS 用 calshow:// 直接打開系統日曆 App，Android 優先嘗試 googlecalendar:// |
| 2026-10-08 | v1.0 | 初始版本，Mobile Friendly Landing Page，包含巴士到站查詢工具 |

---

## License

MIT License
