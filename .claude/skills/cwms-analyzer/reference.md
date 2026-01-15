# CWMS Analyzer 技術參考文件

> 本文件包含 CWMS 優化分析 Skill 的詳細技術規範與實作指南。

---

## 一、3-File Pattern 架構說明

### 1.1 設計理念

基於 **Manus AI Context Engineering** 原則：

> "The File System is the Memory"
> 「檔案系統即是記憶」

**核心概念**：
- AI 無法保留長期記憶，但檔案可以
- 將 AI 的思考過程外化為檔案
- 每個檔案都是可編輯、可驗證的中間產物
- 人類可介入任何階段進行校正

### 1.2 三檔案對應關係

| 原始 Pattern | CWMS 對應 | 用途 |
|:---|:---|:---|
| `task_plan.md` | `CWMS流程優化對比圖.md` | 任務規劃與流程設計 |
| `findings.md` | `CWMS優化報告_痛點與流程分析.md` | 分析發現與洞察 |
| `progress.md` | `CWMS優化項目統計分析.xlsx` | 進度追蹤與狀態 |

### 1.3 為何需要中間文檔？

**AI 對齊問題**：
- AI 可能誤解 Excel 數據含義
- AI 可能遺漏重要上下文
- AI 可能做出錯誤假設

**解決方案**：
- 生成可讀的中間文檔
- 人類審核確認 AI 理解正確
- 修正後再進入下一階段

---

## 二、Excel 數據處理規範

### 2.1 支援的 Excel 格式

- `.xlsx` (推薦)
- `.xls` (舊版相容)
- `.csv` (純文字)

### 2.2 必要欄位映射

```python
COLUMN_MAPPING = {
    # 標準欄位名 -> 可能的變體
    'item_id': ['項目編號', '編號', 'ID', '序號'],
    'item_name': ['項目名稱', '優化項目', '名稱', '說明'],
    'status': ['目前狀態', '狀態', '進度', '完成度'],
    'priority': ['優先級', '優先順序', '等級', 'P等級'],
    'owner': ['負責人', '承辦人', '負責單位', '部門'],
    'start_date': ['開始日期', '起始日', '開工日'],
    'end_date': ['預計完成日', '結束日期', '完成日', '目標日期'],
    'department': ['相關部門', '部門', '單位'],
    'pain_level': ['痛點等級', '影響程度', '嚴重度']
}
```

### 2.3 狀態值標準化

```python
STATUS_MAPPING = {
    '已完成': ['已完成', '完成', 'Done', 'Completed', '100%'],
    '進行中': ['進行中', '執行中', 'In Progress', 'WIP'],
    '待處理': ['待處理', '待辦', 'Pending', 'TODO', '未開始'],
    '暫緩': ['暫緩', '延後', 'On Hold', 'Delayed']
}
```

### 2.4 日期格式處理

```python
SUPPORTED_DATE_FORMATS = [
    '%Y-%m-%d',      # 2026-01-15
    '%Y/%m/%d',      # 2026/01/15
    '%d/%m/%Y',      # 15/01/2026
    '%m/%d/%Y',      # 01/15/2026
    '%Y年%m月%d日',  # 2026年01月15日
]
```

---

## 三、SVG 圖表技術規範

### 3.1 為何選擇 SVG 而非 Mermaid.js？

**Mermaid.js 問題**：
- 中文文字寬度計算異常
- 產生 `<rect> attribute width: A negative value is not valid` 錯誤
- 複雜甘特圖渲染不穩定

**SVG 優勢**：
- 完全控制圖形渲染
- 中文文字處理穩定
- 單檔案部署友好
- 無外部依賴

### 3.2 甘特圖 SVG 結構

```svg
<svg width="1100" height="520" viewBox="0 0 1100 520">
    <!-- 1. 背景層 -->
    <rect x="0" y="0" width="1100" height="520" fill="#f8f9fa" rx="10"/>

    <!-- 2. 時間軸層 -->
    <g class="timeline">
        <!-- 月份標記 -->
        <text x="210" y="50" font-size="14" fill="#333">1月</text>
        <line x1="210" y1="60" x2="210" y2="500" stroke="#ddd" stroke-width="1"/>
    </g>

    <!-- 3. 任務層 -->
    <g class="tasks">
        <!-- 任務條 -->
        <rect x="210" y="115" width="70" height="20" fill="#4CAF50" rx="3"/>
        <!-- 任務標籤 -->
        <text x="10" y="130" font-size="12" fill="#333">任務名稱</text>
    </g>

    <!-- 4. 標記層 -->
    <g class="markers">
        <!-- 今日標記線 -->
        <line x1="380" y1="70" x2="380" y2="500"
              stroke="#F44336" stroke-width="2" stroke-dasharray="5,5"/>
        <text x="380" y="65" text-anchor="middle" font-size="10" fill="#F44336">今日</text>
    </g>
</svg>
```

### 3.3 流程圖 SVG 結構

```svg
<svg width="800" height="400" viewBox="0 0 800 400">
    <!-- 定義箭頭標記 -->
    <defs>
        <marker id="arrowhead" markerWidth="10" markerHeight="7"
                refX="9" refY="3.5" orient="auto">
            <polygon points="0 0, 10 3.5, 0 7" fill="#666"/>
        </marker>
    </defs>

    <!-- 節點 -->
    <rect x="50" y="150" width="120" height="50" fill="#E3F2FD"
          stroke="#2196F3" stroke-width="2" rx="5"/>
    <text x="110" y="180" text-anchor="middle" font-size="12">步驟名稱</text>

    <!-- 連接線 -->
    <path d="M170 175 L230 175" stroke="#666" stroke-width="2"
          marker-end="url(#arrowhead)"/>

    <!-- 決策菱形 -->
    <polygon points="400,130 450,175 400,220 350,175" fill="#FFF3E0"
             stroke="#FF9800" stroke-width="2"/>
    <text x="400" y="180" text-anchor="middle" font-size="11">判斷？</text>
</svg>
```

### 3.4 顏色規範

```css
/* 狀態顏色 */
--completed: #4CAF50;   /* 綠色 - 已完成 */
--in-progress: #2196F3; /* 藍色 - 進行中 */
--pending: #FFC107;     /* 黃色 - 待處理 */
--delayed: #9E9E9E;     /* 灰色 - 暫緩 */
--urgent: #F44336;      /* 紅色 - 緊急 */

/* 流程圖顏色 */
--asis-bg: #FFF3E0;     /* 橙色背景 - AS-IS */
--asis-border: #FF9800;
--tobe-bg: #E8F5E9;     /* 綠色背景 - TO-BE */
--tobe-border: #4CAF50;
```

---

## 四、Chart.js 配置參考

### 4.1 圓餅圖（狀態分布）

```javascript
const statusChart = new Chart(ctx, {
    type: 'pie',
    data: {
        labels: ['已完成', '進行中', '待處理', '暫緩'],
        datasets: [{
            data: [25, 45, 20, 10],
            backgroundColor: ['#4CAF50', '#2196F3', '#FFC107', '#9E9E9E'],
            borderWidth: 2,
            borderColor: '#fff'
        }]
    },
    options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
            legend: {
                position: 'bottom',
                labels: {
                    font: { family: "'Microsoft JhengHei', sans-serif" }
                }
            },
            tooltip: {
                callbacks: {
                    label: (ctx) => `${ctx.label}: ${ctx.raw} 項 (${ctx.percent}%)`
                }
            }
        }
    }
});
```

### 4.2 長條圖（優先級分布）

```javascript
const priorityChart = new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['P1 緊急', 'P2 重要', 'P3 一般'],
        datasets: [{
            label: '項目數量',
            data: [15, 30, 20],
            backgroundColor: ['#F44336', '#FF9800', '#4CAF50']
        }]
    },
    options: {
        indexAxis: 'x',  // 垂直長條
        plugins: {
            legend: { display: false }
        },
        scales: {
            y: {
                beginAtZero: true,
                ticks: { stepSize: 5 }
            }
        }
    }
});
```

### 4.3 折線圖（趨勢分析）

```javascript
const trendChart = new Chart(ctx, {
    type: 'line',
    data: {
        labels: ['1月', '2月', '3月', '4月', '5月', '6月'],
        datasets: [{
            label: '完成項目',
            data: [5, 8, 12, 15, 20, 25],
            borderColor: '#4CAF50',
            backgroundColor: 'rgba(76, 175, 80, 0.1)',
            fill: true,
            tension: 0.3
        }]
    },
    options: {
        plugins: {
            legend: { position: 'top' }
        },
        scales: {
            y: { beginAtZero: true }
        }
    }
});
```

---

## 五、部署規範

### 5.1 GitHub Pages 部署

```bash
# 1. 確保 index.html 在根目錄
ls -la index.html

# 2. Git 提交
git add index.html
git commit -m "feat: 更新 CWMS 優化追蹤儀表板"

# 3. 推送到 GitHub
git push origin main

# 4. 啟用 GitHub Pages（首次）
gh api -X POST /repos/{owner}/{repo}/pages \
  --input - <<< '{"source":{"branch":"main","path":"/"}}'

# 5. 確認部署狀態
gh api /repos/{owner}/{repo}/pages
```

### 5.2 單檔案要求

**必須滿足**：
- 所有 CSS 內嵌於 `<style>` 標籤
- Chart.js 透過 CDN 載入
- 所有 SVG 直接內嵌
- 無需後端伺服器

### 5.3 CDN 備援

```html
<!-- 主要 CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<!-- 備援 CDN -->
<script>
if (typeof Chart === 'undefined') {
    document.write('<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"><\/script>');
}
</script>
```

---

## 六、AI 對齊驗證清單

### 6.1 階段一驗證（Excel 解析）

```
□ 欄位名稱正確識別
□ 數據筆數與來源一致
□ 日期格式正確解析
□ 狀態值正確分類
□ 優先級排序合理
□ 無遺漏數據
```

### 6.2 階段二驗證（中間文檔）

```
□ 流程圖反映實際作業流程
□ 痛點描述符合實際情況
□ 痛點優先級排序合理
□ 解決方案具可行性
□ 統計數據計算正確
□ 無虛構或假設內容
```

### 6.3 階段三驗證（可視化輸出）

```
□ 所有圖表正確渲染
□ 數據與中間文檔一致
□ SVG 無渲染錯誤
□ Tab 切換正常
□ 響應式設計正常
□ 跨瀏覽器相容
```

---

## 七、故障排解指南

### 7.1 常見問題

| 問題 | 原因 | 解決方案 |
|:---|:---|:---|
| SVG 不顯示 | viewBox 設定錯誤 | 檢查 viewBox 與實際尺寸是否匹配 |
| 中文亂碼 | 編碼問題 | 確保 `<meta charset="UTF-8">` |
| Chart.js 錯誤 | CDN 載入失敗 | 使用備援 CDN |
| 日期解析錯誤 | 格式不支援 | 擴充 `SUPPORTED_DATE_FORMATS` |

### 7.2 除錯技巧

```javascript
// 瀏覽器 Console 除錯
console.log('Chart data:', chartData);
console.log('SVG elements:', document.querySelectorAll('svg'));

// 驗證 Chart.js 載入
if (typeof Chart !== 'undefined') {
    console.log('Chart.js version:', Chart.version);
} else {
    console.error('Chart.js not loaded!');
}
```

---

**文件版本**: v1.0
**最後更新**: 2026-01-15
**參考來源**: planning-with-files, Manus AI Context Engineering
