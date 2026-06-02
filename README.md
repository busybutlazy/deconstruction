# Architecture Teardowns

> 一個系統架構師的養成筆記 —— 拆解大型開源專案的架構、決策與取捨。
>
> **核心信念:** 工具能產出「地圖」,但架構師的價值在於「判讀地形的判斷力」。
> 這裡累積的不是圖,而是**決策評註(decision commentary)**。

## 這是什麼

每進一個陌生的大型 codebase,我用一套固定方法把它拆開,記錄:

- 它的**結構**(分層 / 容器 / 主幹流程)
- 它的**關鍵決策與取捨**(用 ADR 格式:脈絡 → 決策 → 被否決的方案 → 代價)
- 它**為什麼聰明 / 哪裡我會挑戰**
- 我能**偷走複用**的 pattern

用同一套格式拆 10 個專案後,跨專案的重複 pattern 會浮現 —— 那層綜合(見 [`patterns/`](./patterns/))才是 architect-level 思維的證據。

## 方法

借兩個業界框架:

- **C4 model**(Simon Brown)—— 畫結構(Context → Container → Component → Code)
- **ADR / Architecture Decision Record**(Michael Nygard)—— 記每個決策的脈絡與後果

工作流:

```
1. 快速建骨架(工具輔助:/understand 等,或手動讀 entry point + 目錄 + 依賴圖)
2. 鑽透「一條」端到端主幹流程
3. 挖決策點(讀 PR / issue / CHANGELOG / 設計文件)
4. ★ 自己寫 ADR / 取捨 / why-it-won —— 工具做不到的那一半
5. 配圖(Mermaid / 截圖)、發佈
```

⚠️ 工具的產出(summary、edge)當「假設」驗證,不當結論。它說「是什麼」,說不清「為什麼」。

## 前置依賴與安裝

骨架建構(工作流第 1 步)主要靠 **[Understand Anything](https://github.com/Lum1104/Understand-Anything)** 這個 AI agent 插件 —— 它是**強烈建議的輔助工具,不是硬依賴**(沒它也能手動讀 entry point + 目錄 + 依賴圖,只是慢)。

### 1. Node ≥22 + pnpm ≥10(插件的執行前提)

本機無系統級 Node/pnpm,用 **nvm** 安裝(免 sudo、與開發環境一致):

```bash
# 安裝 nvm(若尚未安裝)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
export NVM_DIR="$HOME/.nvm"; [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
# Node 22 + pnpm 10(corepack 隨 Node 內建)
nvm install 22 && nvm alias default 22
corepack enable && corepack prepare pnpm@10 --activate
node -v && pnpm -v        # 應為 v22.x / 10.x
```

> 已裝好,`~/.bashrc` 會自動載入 nvm,新 login shell 內 `node`/`pnpm` 可直接用。備援:本機另有 `bun`(`~/.bun/bin`)與 Cursor 內建 node。

### 2. 安裝 Understand Anything 插件(以 Claude Code 為例)

在 Claude Code 內:

```
/plugin marketplace add Lum1104/Understand-Anything
/plugin install understand-anything
/reload-plugins
```

(其他平台 Codex / Cursor / Copilot / opencode 等的裝法見該 repo README。)

### 3. 首次建置核心套件

插件快取在 `~/.claude/plugins/cache/understand-anything/understand-anything/<version>/`。首次跑 `/understand` 時 skill 會自動建置 `packages/core`;若失敗可手動:

```bash
cd ~/.claude/plugins/cache/understand-anything/understand-anything/<version>
pnpm install && pnpm --filter @understand-anything/core build
```

### 4. 用法速查

```
/understand --language zh [子目錄路徑]   # 建知識圖譜(>100 檔時用子目錄縮範圍)
/understand-dashboard                     # 啟動 Vite 視覺化儀表盤(?token 閘門)
/understand-chat <問題>                   # 用圖譜問答
/understand-explain / -diff / -onboard    # 其他下游 skill
```

> 輸出存於被分析專案的 `.understand-anything/knowledge-graph.json`。詳細執行環境註記見本目錄 `CLAUDE.md`。

## Index

| #  | 專案 | 一句話 | 主要學到的 pattern |
|----|------|--------|--------------------|
| 01 | [Understand Anything](./understand-anything/) | 寄生在 AI agent 裡的 codebase 理解引擎 | 分發即架構 / artifact-vs-session / 中間檔繞 context 上限 |

## 範本

新拆解直接複製 [`TEMPLATE.md`](./TEMPLATE.md) 到 `<project-name>/README.md`。

## 發佈

- 正本放這個 repo,pin 在 GitHub profile。
- 系列文「Architecture Teardown #N: X」發 dev.to / Medium / 個人站。
- 能被記住的永遠是「有觀點的取捨分析」,不是「我讀了 code」。
- 守則:標明用到的工具、尊重原 repo license、別把自動生成內容當成自己的分析掛名。
- (筆記用繁中;要對國際 architect 職缺曝光,建議再出一版 EN。)
