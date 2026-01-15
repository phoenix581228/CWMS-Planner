---
name: cwms-analyzer
description: CWMS優化項目Excel分析與可視化儀表板生成。三階段工作流程：Excel解析分析、中間文檔生成與AI對齊驗證、SVG/Chart.js可視化報表輸出。當進行CWMS分析、Excel上傳分析、優化項目追蹤、甘特圖生成、流程對比圖、痛點分析、可視化儀表板、專案追蹤報表時使用。
---

# CWMS 優化項目分析與可視化 Skill

> 本 Skill 實現從 Excel 數據到可視化儀表板的全自動化工作流程，採用 3-File Pattern 確保 AI 理解對齊。

---

## 第一章：工作流程概述

### 1.1 三階段自動化流程

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   階段一：輸入   │ → │  階段二：處理    │ → │  階段三：輸出   │
│   Excel 分析    │    │  中間文檔生成   │    │  可視化報表    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### 1.2 3-File Pattern 架構

基於 Manus AI Context Engineering 原則，使用檔案系統作為 AI 記憶：

| 檔案 | 用途 | 對應產物 |
|:---|:---|:---|
| `task_plan.md` | 任務規劃與執行計劃 | 專案分析計劃 |
| `findings.md` | 分析發現與洞察 | 痛點分析報告 |
| `progress.md` | 進度追蹤與狀態 | 項目統計分析 |

---

## 第二章：階段一 - Excel 輸入分析

### 2.1 Excel 檔案解析

**輸入格式**：CWMS 優化項目 Excel 檔案

**必要欄位**：
- 項目編號 / 優化項目名稱
- 目前狀態 / 完成度
- 負責人 / 相關部門
- 開始日期 / 預計完成日
- 優先級 / 痛點等級

### 2.2 數據提取流程

```python
# 數據提取範例
import pandas as pd

def extract_cwms_data(excel_path):
    """提取 CWMS 優化項目數據"""
    df = pd.read_excel(excel_path)

    # 分類統計
    status_summary = df['目前狀態'].value_counts()
    priority_summary = df['優先級'].value_counts()

    return {
        'total_items': len(df),
        'status_distribution': status_summary,
        'priority_distribution': priority_summary,
        'raw_data': df
    }
```

### 2.3 AI 對齊驗證點

- [ ] 確認欄位名稱正確對應
- [ ] 驗證日期格式解析正確
- [ ] 檢查狀態分類是否完整
- [ ] 確認數據筆數與來源一致

---

## 第三章：階段二 - 中間文檔生成

### 3.1 必要產出文檔

#### 文檔一：流程優化對比圖 (`CWMS流程優化對比圖.md`)

**內容結構**：
```markdown
# CWMS 流程優化對比圖

## 現行流程 (AS-IS)
[流程圖或 SVG]

## 優化流程 (TO-BE)
[流程圖或 SVG]

## 優化重點說明
- 痛點 1 → 解決方案
- 痛點 2 → 解決方案
```

#### 文檔二：痛點與流程分析報告 (`CWMS優化報告_痛點與流程分析.md`)

**內容結構**：
```markdown
# CWMS 優化報告：痛點與流程分析

## 執行摘要
[關鍵發現總結]

## 痛點清單
| 編號 | 痛點描述 | 影響程度 | 優先級 |
|:---|:---|:---|:---|

## 建議解決方案
[詳細解決方案]
```

#### 文檔三：統計分析 (`CWMS優化項目統計分析.xlsx`)

**必要工作表**：
- 總覽統計
- 狀態分布
- 時程分析
- 部門負責統計

### 3.2 人工審核檢查點

**⚠️ 重要：此階段需人工確認 AI 理解對齊**

```
┌─────────────────────────────────────────┐
│         AI 對齊驗證檢查清單              │
├─────────────────────────────────────────┤
│ □ 流程圖是否正確反映現行作業？           │
│ □ 痛點描述是否符合實際情況？             │
│ □ 優先級排序是否合理？                   │
│ □ 統計數據是否與來源一致？               │
│ □ 解決方案是否可行？                     │
└─────────────────────────────────────────┘
```

---

## 第四章：階段三 - 可視化報表輸出

### 4.1 輸出格式：index.html

**技術棧**：
- **圖表庫**：Chart.js（圓餅圖、長條圖、折線圖）
- **甘特圖**：純 SVG 繪製（避免 Mermaid.js 中文問題）
- **流程圖**：純 SVG 繪製
- **樣式**：內嵌 CSS（單檔案部署）

### 4.2 儀表板結構

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>CWMS 優化項目追蹤儀表板</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* 內嵌樣式 */
    </style>
</head>
<body>
    <!-- 頂部統計卡片 -->
    <section class="stats-cards">...</section>

    <!-- Tab 導航 -->
    <nav class="tabs">...</nav>

    <!-- 圖表區域 -->
    <section class="charts">
        <canvas id="statusChart"></canvas>
        <canvas id="priorityChart"></canvas>
    </section>

    <!-- SVG 甘特圖 -->
    <section class="gantt">
        <svg>...</svg>
    </section>

    <!-- SVG 流程對比圖 -->
    <section class="flowcharts">
        <svg>...</svg>
    </section>
</body>
</html>
```

### 4.3 SVG 甘特圖規範

**⚠️ 重要：使用純 SVG 而非 Mermaid.js**

原因：Mermaid.js 處理中文文字時容易產生 `negative width` 錯誤。

```svg
<svg width="1100" height="520" viewBox="0 0 1100 520">
    <!-- 背景與網格 -->
    <rect x="0" y="0" width="1100" height="520" fill="#f8f9fa"/>

    <!-- 時間軸 -->
    <text x="210" y="50" font-size="14" fill="#333">1月</text>

    <!-- 任務條 -->
    <rect x="210" y="115" width="70" height="20" fill="#4CAF50" rx="3"/>

    <!-- 今日標記線 -->
    <line x1="380" y1="70" x2="380" y2="500"
          stroke="#F44336" stroke-width="2" stroke-dasharray="5,5"/>
</svg>
```

### 4.4 Chart.js 圖表配置

```javascript
// 狀態分布圓餅圖
new Chart(document.getElementById('statusChart'), {
    type: 'pie',
    data: {
        labels: ['已完成', '進行中', '待處理', '暫緩'],
        datasets: [{
            data: [25, 45, 20, 10],
            backgroundColor: ['#4CAF50', '#2196F3', '#FFC107', '#9E9E9E']
        }]
    },
    options: {
        responsive: true,
        plugins: {
            legend: { position: 'bottom' }
        }
    }
});
```

---

## 第五章：自動化執行流程

### 5.1 完整執行步驟

```
1. 📥 接收 Excel 檔案
   └─ 確認檔案路徑與格式

2. 📊 數據解析
   └─ 提取關鍵欄位
   └─ 建立統計摘要

3. 📝 生成中間文檔
   └─ CWMS流程優化對比圖.md
   └─ CWMS優化報告_痛點與流程分析.md
   └─ CWMS優化項目統計分析.xlsx

4. ✅ 人工審核（AI 對齊驗證）
   └─ 確認理解正確
   └─ 修正錯誤解讀

5. 🎨 生成可視化報表
   └─ index.html（含 SVG + Chart.js）

6. 🚀 部署
   └─ GitHub Pages 或其他平台
```

### 5.2 執行命令範例

```bash
# 階段一：分析 Excel
# Claude 會自動讀取並分析 Excel 檔案

# 階段二：生成中間文檔
# Claude 會在指定目錄生成 3 個中間文檔

# 階段三：人工確認後生成報表
# 確認 AI 理解對齊後，生成 index.html

# 階段四：部署到 GitHub Pages
git add index.html
git commit -m "feat: 更新 CWMS 優化追蹤儀表板"
git push origin main
```

---

## 第六章：檢查清單

### ✅ 階段一檢查（Excel 輸入）

- [ ] Excel 檔案路徑正確
- [ ] 必要欄位皆已識別
- [ ] 數據筆數確認無誤
- [ ] 日期格式解析正確

### ✅ 階段二檢查（中間文檔）

- [ ] 流程對比圖反映實際情況
- [ ] 痛點清單完整且優先級合理
- [ ] 統計數據與來源一致
- [ ] 解決方案具可行性

### ✅ 階段三檢查（可視化輸出）

- [ ] 所有圖表正確渲染
- [ ] SVG 甘特圖無負寬度錯誤
- [ ] 數據與中間文檔一致
- [ ] 響應式設計正常

### ✅ 部署檢查

- [ ] 單檔案可獨立運行
- [ ] CDN 資源可正常載入
- [ ] 跨瀏覽器相容性確認
- [ ] GitHub Pages 部署成功

---

## 附錄：常見問題排解

### Q1: Mermaid.js 甘特圖出現負寬度錯誤

**原因**：中文文字導致寬度計算異常

**解決**：改用純 SVG 繪製甘特圖（本 Skill 預設方案）

### Q2: Excel 欄位名稱不一致

**解決**：在階段一建立欄位映射表，統一內部欄位名稱

### Q3: Chart.js CDN 載入失敗

**解決**：使用備用 CDN 或內嵌 Chart.js 原始碼

---

**版本**: v1.0
**最後更新**: 2026-01-15
**來源**: planning-with-files 3-File Pattern + CWMS 專案實踐
**維護者**: CWMS 優化團隊
