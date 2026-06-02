# CLAUDE.md — Architecture Teardowns 知識庫

> 給未來在這個目錄工作的 agent 讀。這不是一個程式專案,是一份**架構拆解知識庫**。請先讀完本檔再動作。

## 這個目錄是什麼

使用者(目標:成長為**系統架構師 systems architect**)用一套固定方法拆解大型開源專案的架構與決策,累積成個人知識庫,並會公開發佈作為履歷的一部分。

**核心信念:工具產出「地圖」,但價值在於「判讀地形的判斷力」。這裡累積的是決策評註(decision commentary),不是自動生成的圖。**

## 目錄結構

```
understand/
├── CLAUDE.md          # 本檔
├── README.md          # 系列說明 + 方法論 + 拆解索引(Index 表)
├── TEMPLATE.md        # 可重複的拆解模板(C4 + ADR);開新篇就複製它
├── patterns/
│   └── README.md      # ★ 跨專案 pattern 索引 —— architect-level 思維的綜合層
└── <project-name>/
    └── README.md      # 一個專案一篇拆解
```

**檔名慣例(使用者已確認,不要改):** 每個資料夾內用 `README.md`(GitHub 點進資料夾會自動渲染它)。**不要**扁平化成 `01-xxx.md`。

## 拆解方法

借兩個業界框架:**C4 model**(結構:Context→Container→Component→Code)+ **ADR / Architecture Decision Record**(決策)。

每篇結構(見 `TEMPLATE.md`):
1. 一句話定位 2. Context 圖(C4 L1) 3. 分層/容器(C4 L2) 4. 主幹流程(挑一條端到端講透)
5. **★ ADR**:每條 = 脈絡 → 決策 → 被否決的替代方案 → 代價/後果 → **佐證(issue/PR/commit,不可憑猜)**
6. **★ 為何聰明 / 我會挑戰什麼** 7. 可偷的 pattern(同步更新 `patterns/`) 8. 風險/演化

## ⚠️ 工作模式 = C(蘇格拉底)—— 最重要的一條

- **#01 `understand-anything/` 是「黃金範本」**,由 AI 全寫,當示範用,已完成,別動。
- **從 #02 起,絕對不要直接幫使用者把分析寫完。** 那會毀掉學習價值與履歷真實性(面試一問取捨就穿幫)。
- 正確分工:
  - **你(agent)做粗活**:跑工具/讀 code 抽結構、查 issue/PR/commit,給出**只有事實、沒有判斷**的骨架 —— Context 圖、分層表、主幹流程填好,但 **ADR 的「決策/取捨」與「批判」段落留白**。
  - **用問題引導使用者自己想/自己寫**那些段落(例:「它選 X 不選 Y,為什麼?代價是什麼?跟 #01 哪個 pattern 像?」)。
  - 使用者寫完 → 你給回饋(補佐證、指盲點、挑戰論點),再一起收進 `patterns/`。
- 學習發生在「使用者讀草稿→不同意→自己重寫」的摩擦裡,不在打字裡。

## 開新拆解的流程

1. 使用者給一個專案(本地路徑或 GitHub URL)。建議挑**和既有篇章不同領域**的,讓 `patterns/` 長出跨領域厚度。
2. 用 **Understand Anything 插件**輔助建骨架:對該專案跑 `/understand`(大型 repo >100 檔時用子目錄縮小範圍,輸出語言用 `--language zh`)。產出在該專案的 `.understand-anything/knowledge-graph.json`,可用 `/understand-dashboard` 視覺化、`/understand-chat` 問答。
3. 複製 `TEMPLATE.md` 到 `<project-name>/README.md`,填入結構性事實,**留白判斷段落**,進入蘇格拉底模式。
4. 完成後更新 `README.md` 的 Index 表與 `patterns/`。

## 環境(執行 /understand 工具鏈用)

- 本機 **沒有系統級 Node/pnpm**;已透過 **nvm 安裝 Node v22.22.3 + corepack pnpm 10.6.2**(由 `~/.bashrc` 載入,login shell 內 `node`/`pnpm` 可直接用)。
- 也有 `bun`(`~/.bun/bin`)與 Cursor 內建 node 可備用。`python` 已軟連到 `~/.local/bin`。
- Understand Anything 插件快取在 `~/.claude/plugins/cache/understand-anything/...`;首次用需在其 `packages/core` 建置(`pnpm install && pnpm --filter @understand-anything/core build`)。

## 輸出語言

- 知識庫工作版用**繁體中文**(技術術語保留英文)。
- 要公開發佈到國際 architect 職缺,另出一版 **英文** 較有曝光。

## 索引

目前篇章見 `README.md` 的 Index 表。#01 = Understand Anything(開發者工具/AI agent 領域)。
