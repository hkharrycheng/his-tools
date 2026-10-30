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
    "deployedPage": "https://hkharrycheng.github.io/bus-eta-sync/bus-eta.html",
    "description": "九巴、城巴/新巴、小巴實時到站時間，常用路線一覽",
    "status": "已上線",
    "tags": ["戶外"]
  },
  {
    "name": "家庭醫療記錄",
    "icon": "🏥",
    "deployedPage": "https://family-health-241.pages.dev",
    "description": "家庭醫療記錄：六大指標儀表板、用藥追蹤、Cici/Coco WHO成長曲線",
    "status": "已上線",
    "tags": ["家庭"]
  }
]
```

### 欄位說明

| 欄位 | 類型 | 說明 |
|------|------|------|
| name | string | 工具名稱（嚴格跟 Register List 的 HIS Tools 欄） |
| icon | string | 工具圖標（emoji 或 logos/*.png） |
| github | string | GitHub repo URL |
| deployedPage | string | 工具部署頁面連結 |
| description | string | 工具描述 |
| status | string | 狀態（已上線/即將推出/開發中） |
| tags | array | 標籤列表，**每次生成必須從 Register List「Tags」欄即時讀取，禁止硬編碼** |
| baseUrl | string | 對應飛書 Base URL |
| logo | string | 可選，本地 logos/ 下嘅圖片檔 |

---

## 文件結構

### GitHub Repository

```
his-tools/
├── index.html              # Landing Page 主頁面
├── tools.json              # 工具列表（由 Register List 生成）
├── logos/                  # 各工具 LOGO 圖片
└── README.md               # 本文件
```

---

## Version Control

### 目前 Tags 集合（2026-10-30）

- **戶外**：巴士到站時間、外幣夾錢、點餐清單、國泰機票助手（4 個）
- **電玩**：貨品監控、Wild Rift、鳴潮、影片資料庫、音樂庫、兒童遊戲（6 個）
- **家庭**：學生成績記錄、升中分析、家庭日記、家庭醫療記錄（4 個）
- **Main**（無 tag，1 個）

> ⚠️ 「學業」tag 已於 2026-10-30 移除，原屬學業嘅學生成績記錄同升中分析已歸入「家庭」。

### 版本記錄

| 版本 | 日期 | 更新內容 |
|------|------|----------|
| v1.09 | 2026-10-30 | 新增 J13「家庭醫療記錄」（family-health-241.pages.dev）；Tags 重新對齊 Register List：學生成績記錄/升中分析由「學業」改為「家庭」，「學業」tag 正式移除 |
| v1.08 | 2026-10-19 | 加入「家庭日記」工具卡片（Cloudflare Pages：memories-blogger.pages.dev）；tools.json 重新生成 |
| v1.07 | 2026-10-13 | 統一圖標 LOGO（tools.json logo 字段 + Main 卡片顯示圖標相片） |
| v1.06 | 2026-10-12 | 國泰機票助手卡片改用 Cathay 官方 LOGO 取代 ✈️ emoji |
| v1.05 | 2026-10-12 | 加入「鳴潮助手」同「國泰機票助手」工具卡片 |
| v1.04 | 2026-10-12 | 加入 notranslate 禁止瀏覽器翻譯；tools.json 改為純數組 |
| v1.03 | 2026-10-11 | 簡化為一個「📊 多維表格」按鈕（智能跳轉）；J06改用新「AI補習社」多維表格 |
| v1.02 | 2026-10-11 | 工具分類 + AI 評價；多維表格改用飛書 APP deeplink |
| v1.01 | 2026-10-09 | Tag 分組 + toggle 功能；動態讀取 tools.json |
| v1.00 | 2026-10-08 | 初始版本，Mobile Friendly Landing Page |

### 版本規則

- 初始版本：v1.00
- 每次更新 +0.01
- tools.json 嘅 tags **必須每次從 Register List 即時讀取**，唔可以死記舊 tag 集合（例如 2026-10-30 教訓：「學業」被移除後仍硬編碼導致 misalign）

---

## 常見問題

### Q: 點解天氣顯示唔到？

A: 可能原因：網絡問題、Open-Meteo API 暫時不可用、瀏覽器阻止跨域請求。

### Q: 點樣新增工具？

A:
1. 在飛書多維表格「Register List」中新增工具記錄
2. 填寫 HIS Tools、Deployed Page、圖標、描述、Tags 等欄位
3. 呼叫 J00 生成 tools.json 並上傳到 GitHub
4. HTML 會自動從 tools.json 動態載入工具列表

### Q: Tags 分組點樣運作？

A: 每個工具嘅 Tags 嚴格對應 Register List「Tags」欄（單選）。頁面按 Tags 分組顯示，用戶可 toggle。**新增/移除 tag 選項只需要改 Register List，唔使改 HTML**。

### Q: 點解本地打開 index.html 都用到？

A: 因為 HTML 內嵌咗 DEFAULT_TOOLS 預設數據。當檢測到 file:// 協議時，會直接用內嵌數據；部署到 GitHub Pages 後就會自動 fetch tools.json。

---

## 外部連結

- **Landing Page**: https://hkharrycheng.github.io/his-tools/
- **巴士到站查詢**: https://hkharrycheng.github.io/bus-eta-sync/bus-eta.html
- **家庭醫療記錄**: https://family-health-241.pages.dev
- **家庭日記**: https://memories-blogger.pages.dev
- **音樂庫**: https://music-inventory.pages.dev
- **香港天文台**: https://www.hko.gov.hk/

---

## License

MIT License
