# HIS Tools

Harry's Intelligent Services - 一個 Mobile Friendly 嘅工具集合 Landing Page。

---

## 📋 目錄

1. [ADMIN 使用說明](#admin-使用說明)
2. [Logic Flow](#logic-flow)
3. [Business Flow](#business-flow)
4. [文件結構](#文件結構)
5. [常見問題](#常見問題)

---

## ADMIN 使用說明

### 前置條件

- GitHub Account（用於存放程式同 GitHub Pages 託管）
- 本地 OneDrive 目錄：`D:\OneDrive\Harry Information System\豆包Agents Program Backup\Main\`

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

1. 在 `index.html` 嘅 `.tool-grid` 中新增工具卡片
2. 加入工具名稱、描述、圖標、連結
3. 更新搜尋用嘅 `data-name` 同 `data-desc`
4. 本地測試
5. 執行 J02M 上傳

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
  頁面就緒
        │
        ├─ 點擊日期 → 打開 Google Calendar
        ├─ 點擊天氣 → 打開香港天文台
        ├─ 點擊工具卡片 → 打開對應工具
        └─ 輸入搜尋 → 即時過濾工具卡片
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

## 文件結構

### GitHub Repository

```
his-tools/
├── index.html              # Landing Page 主頁面
├── citybus-logo.png        # 城巴 Logo
└── README.md               # 本文件
```

### 本地 OneDrive 目錄

```
D:\OneDrive\Harry Information System\豆包Agents Program Backup\Main\
├── index.html              # Landing Page 主頁面（本地開發版）
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

## 常見問題

### Q: 點解天氣顯示唔到？

A: 可能原因：
1. 網絡問題 → 檢查網絡連接
2. Open-Meteo API 暫時不可用 → 稍後重試
3. 瀏覽器阻止跨域請求 → 檢查瀏覽器設定

### Q: 點樣新增工具？

A: 
1. 在 `index.html` 嘅 `.tool-grid` 中複製一個工具卡片
2. 修改工具名稱、描述、圖標、連結
3. 更新 `data-name` 同 `data-desc` 用於搜尋
4. 本地測試
5. 執行 J02M 上傳到 GitHub

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

A: 支援中英文搜尋。比對工具名稱（`data-name`）同描述（`data-desc`），大小寫不敏感。

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
