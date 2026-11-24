# AGENTS.md — LLM Wiki Schema for Academic Papers

This file is the **schema and operating manual** for this wiki. Read it at the start of every session before doing anything else. It tells you how the wiki is structured, what conventions to follow, and exactly what to do for each operation.

---

## 1. Directory Structure

```
llm wiki/
├── AGENTS.md              ← 本檔案，你的操作手冊
├── raw/
│   ├── papers/            ← 原始 PDF 論文（唯讀，不可修改）
│   └── assets/            ← 論文圖片、附件
└── wiki/
    ├── index.md           ← 所有頁面的目錄（每次 ingest 後更新）
    ├── log.md             ← 時序操作日誌（append-only）
    ├── my-research.md     ← 【使用者維護】研究方向與關鍵詞定義
    ├── papers/            ← 每篇論文一個頁面
    ├── concepts/          ← 技術概念 / 方法頁面
    ├── topics/            ← 自由生長的主題聚合頁面
    ├── authors/           ← 作者頁面（選擇性建立）
    ├── comparisons/       ← Benchmark 比較表、方法對比
    └── analyses/          ← 深度分析、synthesis 頁面
```

> **唯讀原則**：`raw/` 目錄下的所有檔案你只能讀取，永遠不可修改或刪除。

---

## 2. My Research Direction（關聯性評估基準）

**每次 ingest 前，先讀 `wiki/my-research.md`**，以此作為評估論文相關性的基準。

相關性評分規則（寫入論文頁面 frontmatter）：
- `relevance: high` — 直接使用的方法、直接 baseline 或直接相關主題
- `relevance: medium` — 間接相關、背景知識、可能有用
- `relevance: low` — 領域相鄰、參考用、目前不相關

---

## 3. Page Formats

### 3.1 論文頁面（`wiki/papers/<slug>.md`）

Slug 命名規則：`<year>-<first-author-lastname>-<2~3 word title abbreviation>`
例如：`2024-vaswani-attention-is-all.md`

```markdown
---
title: "論文完整英文標題"
slug: 2024-lastname-short-title
year: 2024
authors: ["Author A", "Author B"]
venue: "NeurIPS 2024"          # 或 arXiv:2401.XXXXX
url: "https://arxiv.org/abs/..."
pdf: "raw/papers/filename.pdf"  # 若有本地 PDF
tags: []                        # 自由標籤，對應 topics/ 頁面名稱
relevance: high                 # high / medium / low
confidence: 0.85                # 0.0–1.0，摘要信心分數（見第 4 節）
needs_review: false             # true 若信心 < 0.7
ingested_at: "2026-11-24"
source_type: paper              # paper / blog / webpage
---

## TL;DR
（1–2 句話，說明這篇論文解決什麼問題、核心答案是什麼）

## 研究動機與問題定義
（Why: 為什麼做這個研究，現有方法有什麼不足）

## 方法 / 技術貢獻
（How: 核心方法、架構、演算法，重要公式用英文保留）

## 實驗結果
（What: 主要 benchmark 結果，與 baseline 比較，重要數字）

## 優點與限制
- **優點**：
- **限制 / Future Work**：

## 與我的研究的關聯
（根據 my-research.md 評估，說明具體關聯點）

## 關鍵引用
（這篇論文引用了哪些重要前作，對應 wiki 中是否有頁面）

## 相關 Wiki 頁面
- [[concepts/xxx]] — 關聯說明
- [[topics/xxx]] — 關聯說明
- [[papers/xxx]] — 關聯說明
```

---

### 3.2 概念頁面（`wiki/concepts/<concept-name>.md`）

概念名稱用英文小寫 kebab-case，例如：`attention-mechanism.md`

```markdown
---
title: "Concept Name"
type: concept
tags: []
source_papers: []   # 引入此概念的論文 slug 列表
updated_at: "2026-11-24"
---

## 定義
（用中文清楚定義這個概念）

## 技術細節
（核心數學、算法描述，英文術語保留）

## 演進歷史
（此概念如何發展，關鍵節點）

## 相關論文
（列出 wiki 中使用或討論此概念的論文）

## 相關概念
- [[concepts/xxx]] — 關係說明
```

---

### 3.3 主題頁面（`wiki/topics/<topic-name>.md`）

主題由 LLM **自由生長**建立，不預設。當 3 篇以上論文共享一個明確主題時，建立主題頁。

```markdown
---
title: "Topic Name"
type: topic
paper_count: 0
updated_at: "2026-11-24"
---

## 主題概述

## 本 Wiki 收錄論文
| 論文 | 年份 | 貢獻摘要 | 相關性 |
|------|------|---------|--------|

## 開放問題 / 研究缺口

## 相關主題
```

---

### 3.4 比較頁面（`wiki/comparisons/<name>.md`）

用於 benchmark 比較或方法對比。

```markdown
---
title: "Comparison: X vs Y vs Z"
type: comparison
papers: []
updated_at: "2026-11-24"
---

## 比較維度

| 方法 | 論文 | 核心思路 | Dataset | Metric | Score | 備注 |
|------|------|---------|---------|--------|-------|------|

## 分析結論
```

---

### 3.5 分析頁面（`wiki/analyses/<name>.md`）

由 Query 操作產出的深度分析，或 Lint 時發現的 synthesis。

```markdown
---
title: "Analysis: ..."
type: analysis
source_papers: []
created_at: "2026-11-24"
---

## 分析問題

## 綜合發現

## 矛盾 / 未解之謎

## 延伸閱讀建議
```

---

## 4. 信心分數（Confidence Score）

每篇論文 ingest 後，評估你對摘要品質的信心，寫入 `confidence` 欄位（0.0–1.0）：

| 情況 | 信心範圍 |
|------|---------|
| 純文字論文，讀取完整 | 0.85–1.0 |
| 論文含大量圖表/公式，部分理解 | 0.65–0.84 |
| 網頁抓取，內容可能不完整 | 0.5–0.7 |
| URL 無法抓取，僅有標題/摘要 | 0.0–0.49 |

當 `confidence < 0.7` 時，自動設定 `needs_review: true`，並在 `index.md` 的 `📋 Needs Review` 區塊列出。

---

## 5. Operations

### 5.1 INGEST（新增來源）

**批次模式**（預設）：當使用者提供多個來源（PDF 路徑、URL、或論文資訊）時，依序處理每一篇。

**每篇論文的 ingest 流程：**

```
Step 1: 讀取來源
  - PDF → 讀取全文
  - URL → 使用 web search 抓取頁面標題、摘要、主要內容
  - 部落格 → 同 URL，額外提取作者觀點與重要引用

Step 2: 評估相關性
  - 讀取 wiki/my-research.md
  - 決定 relevance（high/medium/low）並說明理由

Step 3: 計算信心分數
  - 根據第 4 節規則評估 confidence
  - 若 < 0.7，標記 needs_review: true

Step 4: 建立論文頁面
  - 寫入 wiki/papers/<slug>.md
  - 填寫所有 frontmatter 欄位

Step 5: 更新概念頁面
  - 識別論文中的關鍵技術概念
  - 若概念頁面不存在且此概念重要 → 建立 wiki/concepts/<name>.md
  - 若概念頁面已存在 → 在「相關論文」區塊追加此論文

Step 6: 更新主題頁面
  - 識別論文屬於哪些主題
  - 若主題頁面不存在且已有 ≥3 篇同主題論文 → 建立 wiki/topics/<name>.md
  - 若主題頁面已存在 → 更新論文列表與 paper_count

Step 7: 更新 my-research.md
  - 在「相關論文」清單中，依相關性分類追加此論文

Step 8: 更新 index.md
  - 在對應分類下新增條目（論文標題、slug、一行摘要、相關性）

Step 9: 寫入 log.md
  - 格式：## [YYYY-MM-DD] ingest | <論文標題>
  - 內容：簡述新增了哪些頁面、發現了什麼關聯
```

**批次完成後**，輸出一個總結表格：

| 論文 | Slug | Relevance | Confidence | Needs Review | 新增頁面 |
|------|------|-----------|------------|--------------|---------|

---

### 5.2 QUERY（查詢）

**流程：**
1. 讀取 `wiki/index.md` 找出相關頁面
2. 讀取相關的 papers/、concepts/、topics/ 頁面
3. 綜合回答，附上 wiki 內部連結作為引用
4. **判斷是否值得存檔**：若回答涉及新的比較、synthesis、或深度分析 → 主動建議寫入 `wiki/analyses/` 或 `wiki/comparisons/`

---

### 5.3 LINT（健康檢查）

執行以下檢查，輸出報告：

1. **孤兒頁面**：沒有任何其他頁面連結到的頁面
2. **矛盾偵測**：不同論文頁面中對同一概念有相互矛盾的描述
3. **缺少概念頁**：被多篇論文提及但尚無 `wiki/concepts/` 頁面的術語
4. **過時頁面**：某論文頁面的 benchmark 結果已被更新論文超越
5. **Needs Review 清單**：列出所有 `needs_review: true` 的論文
6. **研究缺口**：根據現有論文，建議還有哪些方向值得深入

```
## [YYYY-MM-DD] lint
Lint 報告：
- 孤兒頁面：X 個
- 矛盾：X 個
- 缺少概念頁：X 個
- Needs Review：X 個
建議新閱讀方向：...
```

---

## 6. Index 與 Log 格式

### index.md 結構

```
# Wiki Index

## 📋 Needs Review（需要人工覆核）
...

## 📄 Papers（論文）
### High Relevance
### Medium Relevance
### Low Relevance

## 🧠 Concepts（概念）

## 🗂️ Topics（主題）

## 📊 Comparisons（比較）

## 🔍 Analyses（分析）
```

每個條目格式：
```
- [[papers/slug]] — 一行摘要 | `relevance: high` | `confidence: 0.9`
```

### log.md 格式

```markdown
## [YYYY-MM-DD] ingest | 論文標題
- 新增論文頁面：wiki/papers/slug.md
- 新增/更新概念：concept-a, concept-b
- 新增/更新主題：topic-x
- Relevance: high | Confidence: 0.85
```

---

## 7. 命名與連結規範

- **所有 wiki 內部連結**使用 Obsidian wiki-link 格式：`[[papers/slug]]`
- **頁面檔名**：英文小寫、kebab-case，不含特殊字元
- **frontmatter**：YAML 格式，字串加引號，列表用 `[]`
- **中英混合**：標題、slug、frontmatter key 用英文；正文說明用中文；技術術語、公式、模型名稱保留英文

---

## 8. Session 開始流程

**每次新 session 開始時，依序執行：**

1. 讀取本檔案（AGENTS.md）
2. 讀取 `wiki/log.md` 最後 10 條（了解最近做了什麼）
3. 讀取 `wiki/my-research.md`（確認當前研究方向）
4. 讀取 `wiki/index.md`（掌握 wiki 當前狀態）
5. 回報：「Wiki 目前有 X 篇論文、Y 個概念頁、Z 個主題頁。最近一次操作：...」

---

## 9. 重要原則

- **不要重新發明已知內容**：ingest 前先查 index.md，同一篇論文不重複建立頁面
- **相關性評估要誠實**：不要為了讓使用者高興而虛報 `high`，低相關就寫 `low`
- **信心分數要保守**：寧可低估信心、標記 needs_review，也不要給出錯誤資訊
- **每次 ingest 都更新 index.md 和 log.md**：這兩個檔案是 wiki 的導航系統，缺一不可
- **主題頁面自由生長**：不要預設分類，讓主題從論文內容中自然浮現
- **好的 Query 答案要存檔**：有價值的分析不應消失在對話歷史中

---

## 10. 衝突偵測規則（Conflict Detection）

**每次 ingest 新資料後，必須執行以下衝突偵測步驟：**

### Step A：掃描現有概念頁面

1. 讀取 `wiki/index.md` 中列出的所有 `wiki/concepts/` 頁面
2. 對照新 ingest 的內容，逐一檢查：
   - 新來源的描述是否與現有概念頁面的「定義」或「技術細節」有**數值矛盾**（如：蛋白質建議量 A 論文說 1.6g/kg，新論文說 2.2g/kg）
   - 新來源的結論是否與現有論文頁面有**方向性矛盾**（如：A 說 HIIT 優於 LISS，新論文說 LISS 在長期脂肪燃燒更有效）

### Step B：記錄衝突

若發現衝突，依嚴重程度處理：

| 衝突類型 | 處理方式 |
|---------|---------|
| **數值不一致**（不同研究族群/條件） | 在概念頁面「技術細節」加入條件說明，不視為真正矛盾 |
| **方向性矛盾**（相同條件、相反結論） | 在概念頁面新增「⚠️ 爭議」段落，列出雙方論文與論點 |
| **新研究明確推翻舊研究** | 更新概念頁面主要說明，將舊說法移至「歷史觀點」段落 |

### Step C：更新紀錄

- 在該 ingest 的 `log.md` 條目中加入：`⚠️ 衝突偵測：發現 X 個衝突，已更新 concept/xxx.md`
- 若無衝突：`✅ 衝突偵測：無衝突`
- 在 `index.md` 有爭議的概念條目後標記 `⚠️`
