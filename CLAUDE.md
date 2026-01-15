# CWMS Planner 專案說明

## 專案概述

CWMS 優化項目追蹤儀表板 - Excel 分析到可視化報表自動化流程

## 技術棧

- **前端**：HTML + CSS + Chart.js + 純 SVG
- **部署**：GitHub Pages
- **AI Agent**：Claude Agent SDK (Python) - 規劃中

## GitHub Repos

| Repo | 說明 | 類型 |
|:---|:---|:---|
| [CWMS-Planner](https://github.com/phoenix581228/CWMS-Planner) | 可視化儀表板 | 公開 |
| [cwms-analyzer-skill](https://github.com/phoenix581228/cwms-analyzer-skill) | Skill 定義 | 私有 |

## 線上預覽

https://phoenix581228.github.io/CWMS-Planner/

---

## 📝 書記記憶 UID（OpenMemory）

### 最新進度記錄（2026-01-15）

| 記憶 UID | 說明 |
|:---|:---|
| `d952f56d-baca-499e-bcb7-db0bae5e21ce` | 每日進度 - 2026-01-15 |
| `8d3e42e8-5cb3-41c4-b6e1-b66ad91e1d29` | 專案完整進度 |

### 查詢標籤

- `daily-progress` - 每日進度
- `CWMS_Planner` - 專案相關
- `claude-agent-sdk` - SDK 研究
- `architecture` - 架構決策

---

## 📅 明日工作項目

1. **POC 驗證**：安裝 `claude-agent-sdk` 並測試基本功能
2. **實作 parse_excel 工具**：Excel 解析自訂 MCP 工具
3. **實作 create_dashboard 工具**：儀表板生成工具
4. **整合測試**：完整工作流程端到端測試

---

## 專案結構

```
CWMS_Planner/
├── CLAUDE.md                  # 本檔案
├── index.html                 # 可視化儀表板（已部署）
└── .claude/skills/cwms-analyzer/
    ├── SKILL.md              # Skill 定義
    ├── reference.md          # 技術參考
    ├── CWMS_AGENT_ARCHITECTURE_PLAN.md  # Agent 架構規劃
    └── templates/            # 範本檔案
```

---

**最後更新**: 2026-01-15
