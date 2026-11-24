# Wiki Log

> Append-only 操作日誌。每次 ingest、query、lint 都在此留下記錄。
> 格式：`## [YYYY-MM-DD] <操作類型> | <標題>`

---

## [2026-11-24] init | Wiki 初始化

- 建立 wiki 目錄結構
- 建立 AGENTS.md schema
- 建立 wiki/index.md、wiki/log.md、wiki/my-research.md
- 設計決策：
  - 語言：中英混合（標題英文、說明中文）
  - 相關性：自動評分 + my-research.md 交叉索引
  - Ingest 模式：全自動批次
  - 主題分類：自由生長
  - 信心分數：< 0.7 標記 needs_review
  - 網頁來源：LLM web search 抓取

---

## [2026-11-24] ingest | 批次 5 篇｜健身與飲食主題

### 新增論文頁面（5 篇）

1. `wiki/papers/2024-bettermilk-muscle-gain-fat-loss.md` — Relevance: high | Confidence: 0.60 | ⚠️ needs_review
2. `wiki/papers/2024-gogonuts-foods-build-muscle.md` — Relevance: medium | Confidence: 0.55 | ⚠️ needs_review
3. `wiki/papers/2024-lhped-calorie-protein-needs.md` — Relevance: high | Confidence: 0.60 | ⚠️ needs_review
4. `wiki/papers/2024-minimark-pre-exercise-nutrition.md` — Relevance: high | Confidence: 0.60 | ⚠️ needs_review
5. `wiki/papers/2024-cmuh-exercise-nutrition-timing.md` — Relevance: high | Confidence: 0.70 ✅

### 新增概念頁面（4 個）

- `wiki/concepts/protein-intake.md` — 蛋白質攝取量（4 篇論文來源）
- `wiki/concepts/nutrient-timing.md` — 運動前中後營養時機（3 篇論文來源）
- `wiki/concepts/energy-balance.md` — 熱量平衡 TDEE/BMR（2 篇論文來源）
- `wiki/concepts/glycogen.md` — 肝醣補充策略（3 篇論文來源）

### 新增主題頁面（2 個）

- `wiki/topics/protein-nutrition.md` — 4 篇論文
- `wiki/topics/peri-workout-nutrition.md` — 3 篇論文

### ✅ 衝突偵測：無重大衝突

**檢查結果**：
- **蛋白質數字**：bettermilk（1.1/1.6–2.2 g/kg）與 cmuh（0.2–0.4 g/kg 運動後單次）不衝突，為不同層次的建議（每日 vs 單次補充）
- **運動後黃金期**：bettermilk（0–45 分鐘）vs cmuh（30–60 分鐘，最多 2 小時）→ 數值差異小，不同條件描述，已在 `concepts/nutrient-timing.md` 中並列說明
- **空腹運動**：cmuh 明確反對（消耗肌肉）；其他文章未提，無正面衝突
- **結論一致性**：5 篇文章核心觀點（蛋白質重要、碳水供能、前後補充有益）一致，無方向性矛盾
