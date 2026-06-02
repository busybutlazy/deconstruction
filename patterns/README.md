# Cross-Project Patterns

> 跨專案重複出現的架構決策。每拆一個新專案,把它印證/挑戰的 pattern 連進來。
> **這一層的綜合,是 architect-level 思維最直接的證據** —— 它證明你不只讀懂單一專案,而是能歸納出可遷移的設計原則。

格式:每個 pattern 記「問題 → 解法 → 取捨 → 哪些專案用了它」。

---

## P1 — 分發即架構(Distribution as Architecture)
- **問題:** 一個技術上合理的工具,如何取得採用?最大的摩擦往往不是功能,是「要不要離開現有工作流」。
- **解法:** 不做獨立 SaaS/App,而是寄生成使用者**已經開著的工具**的擴充(plugin / skill / extension),複用其認證、執行環境、分發渠道。
- **取捨:** 換來零摩擦採用與零基礎設施成本;代價是受制於宿主平台的能力邊界與生命週期,且護城河淺(易被複製)。
- **出現於:** [#01 Understand Anything](../understand-anything/)(Claude Code / Cursor / Copilot plugin,用宿主的 LLM 額度)

## P2 — Artifact 而非 Session
- **問題:** AI 工具的產出常是一次性對話,關掉就沒了,無法版控、無法團隊共享。
- **解法:** 把產出固化成**本地、可版控的檔案**(此例:`knowledge-graph.json`),session 只是生成手段。
- **取捨:** 可複用、可離線、可進 CI、隱私可控;代價是要處理 artifact 的新鮮度(何時過時、如何更新)。
- **出現於:** [#01 Understand Anything](../understand-anything/)(圖譜入 repo + git hook 增量更新)

## P3 — 中間結果落地以繞過 Context 上限
- **問題:** LLM 驅動的多步驟分析,若每步結果都回灌主 agent 的 context,大型輸入會撐爆 context window。
- **解法:** 子任務把結果**寫到磁碟**(中間檔),主流程只傳「指標/路徑」,不傳內容;最後再彙整。
- **取捨:** 換來可處理任意規模;代價是多了檔案 I/O 與中間狀態管理(清理、命名規約、失敗恢復)。
- **出現於:** [#01 Understand Anything](../understand-anything/)(`.understand-anything/intermediate/`,batch-N.json)

## P4 — Hybrid:確定性骨架 + LLM 語意
- **問題:** 純靜態分析的產出沒人看得懂;純 LLM 會幻覺且不穩定。
- **解法:** 用**確定性工具**(parser / tree-sitter)抽骨架(節點、邊),用 **LLM** 補語意(摘要、分層、命名),各司其職。
- **取捨:** 兼得結構正確性與可讀性;代價是兩套機制的接縫(如何把 LLM 輸出對齊到確定性的 ID)需要大量正規化/校驗碼。
- **出現於:** [#01 Understand Anything](../understand-anything/)(tree-sitter 抽結構 + LLM 生摘要,merge 階段大量正規化)

## P5 — Plugin Registry 做語言/能力擴充性
- **問題:** 要支援 N 種語言/格式,核心若 hardcode 就無法擴充。
- **解法:** 定義 extractor/parser 介面 + 一個 registry,每種語言一個外掛;核心對介面編程,不認識具體語言。
- **取捨:** 加語言 = 加一個外掛,核心不動;代價是介面設計要夠通用,且早期抽象成本高。
- **出現於:** [#01 Understand Anything](../understand-anything/)(`PluginRegistry` + 每語言 extractor/parser,~24 語言)
