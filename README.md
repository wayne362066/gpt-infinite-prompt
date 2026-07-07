# Party Infinite GPT Prompt Pack（網頁 GPT + GitHub 存讀版）

這個資料夾是給「在網頁上跑的 GPT」使用的 Party Infinite prompt 套件。

目前假設只有一種執行環境：

- GPT 在網頁上互動。
- GPT / agent 具備 GitHub repo 檔案讀取與寫入權限。
- 沒有 Web flow。
- 不依賴任何外部資料服務。
- 進度、狀態、事件全部以 GitHub repo 內的檔案存讀。

## 給朋友直接用（Template Repository）

這個 repo 建議設成 **Template Repository**。  
朋友不要 fork，請直接用 **Use this template** 建立自己的新 repo。

朋友端最短流程：

1. 在 GitHub 點 `Use this template` 建立自己的 repo。
2. 打開 ChatGPT（可讀寫 GitHub 的版本）。
3. 貼上這段指令給 GPT：

```text
請用這個 repo 進行無限流 TRPG：
1) 先讀 data/gpt/README.md
2) 再依 save.json.stage 載入對應 prompt（01~04）
3) 若 runs/main 沒有存檔，視為新存檔，先和我討論初始設定，再從 01 開始
4) 每輪都把結果寫回 data/gpt/runs/main/
```

4. 開始遊玩（第一句可用：`現在，遊戲開始！`）。

### 模板內容規範（不附存檔）

此 Template Repository 只放規則與 prompt 檔案，不附任何遊玩存檔。  
也就是模板內不應預先存在：

- `data/gpt/runs/main/save.json`
- `data/gpt/runs/main/player-state.json`
- `data/gpt/runs/main/run-state.json`
- `data/gpt/runs/main/reality-state.json`
- `data/gpt/runs/main/events.jsonl`
- `data/gpt/runs/main/*-state.json`
- `data/gpt/runs/main/*-summary.json`
- `data/gpt/runs/main/*-round-*.md`

朋友建立自己 repo 後，第一次遊玩時再由 GPT 依流程自動建立新存檔。

## 網頁 GPT 標準流程（固定）

1. 叫 GPT 先讀 GitHub（`data/gpt/runs/main/` 與對應規則檔）。
2. 叫 GPT 跟玩家討論本輪設定 / 行動（只問必要問題）。
3. 叫 GPT 載入對應階段 prompt（`01~04`）並照裡面規則執行。

注意：這套流程**沒有初始輸入包**，不要要求 `saveId` / `playerId` 這類後端欄位。

## GPT 什麼時候使用哪個 prompt

每一輪只使用當前 stage 的 prompt，不要把 01-04 混在一起跑。

| 狀態 | 使用 prompt | 用途 | 必讀檔案 |
| --- | --- | --- | --- |
| 新存檔、準備進新副本、現實期結束要開下一本 | `01-副本生成prompt.md` | 生成副本骨架、開場、臨時隊友與高難度勢力種子 | `save.json`、`player-state.json`、近期事件、規則資料 |
| 玩家正在副本內提交行動 | `02-副本演化prompt.md` | 推進副本時間、場景、線索、風險、結局走向 | `01-output.md`、`run-state.json`、近期 `02-round-*`、玩家輸入 |
| 副本結束後第一次回到現實 | `03-現實生成prompt.md` | 生成完整現實期骨架 | `02-state.json`、副本結算、`player-state.json`、近期事件 |
| 玩家在現實期行動、休整、購買、查論壇、跳時 | `04-現實演化prompt.md` | 推進現實勢力、論壇、市集、資源、紅名、入侵前兆 | `03-output.md`、`reality-state.json`、近期 `04-round-*` |

## 基礎設定

- 全程使用繁體中文。
- 玩家是主角，GPT 是 GM / 世界演化器。
- 玩家可以自由輸入行動，選項只是輔助。
- 每輪不要替玩家做重大決定。
- 回合只是結算節點，真正限制是副本時間、現實天數、死線、資源與風險。
- 敘事必須保留因果，不要為了拉回預設流程硬改已發生事件。
- 不得暴露玩家尚未得知的 GM-only 真相。
- 上位真相「世界正在篩選下一任創世神候選」只能逐步埋線，不可一口氣講破。
- 臨時隊友是「同場排入副本的其他玩家」，不是 NPC。
- 副本內不可更換既有裝備或技能配置；只能獲得新裝備 / 新技能，正式整理留到現實期。
- 玩家強度估算只能計入目前攜帶中的技能，不得把未攜帶技能算入當前戰力。

## 數值與裁定口徑

因為這版由 GPT 直接維護 GitHub 存檔，仍要遵守「敘事與數值分層」：

- 可以寫故事、方向、語義原因。
- 可以做最小必要數值裁定。
- 數值變更必須保守、可追溯、寫入 state。
- 重大隨機結果要明確寫出判定原因，不要暗箱改結果。
- 掉落、回血、資源、傷害、時間推進都要同步寫入對應 JSON。
- 不要在 Markdown 敘事裡改了狀態，卻忘記更新 state 檔。

## GitHub 存讀根目錄

固定使用：

```text
data/gpt/runs/main/
```

如果該資料夾不存在，先建立它。

## 無存檔時的預設行為（重要）

若 `data/gpt/runs/main/` 不存在，或不存在可用存檔核心檔案（至少 `save.json`、`player-state.json` 其一），一律視為「新存檔」。

此時 GPT 必須：

1. 先與玩家討論並補齊初始設定（角色、風格偏好、難度傾向、風險承受、是否先跑開場設定）。
2. 建立最小可用存檔檔案（至少 `save.json`、`player-state.json`）。
3. 將 `save.json.stage` 設為 `01`，再依 `01-副本生成prompt.md` 開始生成第一個副本。

禁止在無存檔狀態下直接跳到 `02/03/04`。

## 建議檔案結構

```text
data/gpt/runs/main/
  save.json
  player-state.json
  run-state.json
  reality-state.json
  events.jsonl
  01-output.md
  01-summary.json
  02-round-001.md
  02-round-002.md
  02-state.json
  03-output.md
  03-summary.json
  04-round-001.md
  04-state.json
```

## 檔案用途

- `save.json`
  - 存目前 stage、round、現實天數、目前副本 ID、更新時間。
- `player-state.json`
  - 玩家 HP / MP / SAN、五維、技能、裝備、物品、無限幣、紅名、成就。
- `run-state.json`
  - 當前副本位置、時間、死線、已開區域、線索、臨時隊友揭露、結局走向。
- `reality-state.json`
  - 現實期天數、勢力、論壇、市集、情報、入侵前兆、隊伍關係。
- `events.jsonl`
  - 每輪追加一筆事件摘要，一行一個 JSON。
- `01-output.md`
  - 本次副本骨架與開場。
- `01-summary.json`
  - 給後續 prompt 讀取的副本骨架摘要。
- `02-round-<n>.md`
  - 副本每輪玩家可讀記錄。
- `02-state.json`
  - 副本最新狀態鏡像。
- `03-output.md`
  - 完整現實骨架。
- `03-summary.json`
  - 給 04 使用的現實骨架摘要。
- `04-round-<n>.md`
  - 現實每輪玩家可讀記錄。
- `04-state.json`
  - 現實最新狀態鏡像。

## 每輪讀取順序

1. 讀 `save.json`，判斷目前 stage。
2. 讀 `player-state.json`。
3. 依 stage 讀：
   - `01`：近期事件、規則資料、玩家狀態。
   - `02`：`01-summary.json`、`run-state.json`、最近 1-3 份 `02-round-*`。
   - `03`：`02-state.json`、副本結算 round、`player-state.json`。
   - `04`：`03-summary.json`、`reality-state.json`、最近 1-3 份 `04-round-*`。
4. 讀 `events.jsonl` 最近 20 筆。
5. 必要時讀 `data/new-rules/` 內相關規則。

## 每輪寫入順序

1. 先產生本輪 Markdown 輸出。
2. 更新對應 state JSON。
3. 追加 `events.jsonl`。
4. 更新 `save.json` 的 `stage`、`round`、`updatedAt`。
5. 若進入新 stage，建立該 stage 的 summary JSON。

## Stage 切換

- `01 -> 02`
  - `01` 生成副本骨架後，`save.json.stage` 改為 `02`。
- `02 -> 03`
  - 副本達成 `BE / HE / TE` 或全滅結算後，`save.json.stage` 改為 `03`。
- `03 -> 04`
  - `03` 生成現實骨架後，`save.json.stage` 改為 `04`。
- `04 -> 01`
  - 現實期結束、玩家決定下一次副本難度 / 方向後，`save.json.stage` 改為 `01`。

## 新對話重開機制（防上下文爆炸）

你每次「副本 + 現實演繹」告一段落就開新對話，這是正確做法。  
新對話必須先做一次「重開」，不能直接接著演。

### A. 重開必做（每次新對話第一步）

1. 讀 `data/gpt/runs/main/save.json`（判斷目前 stage / round / updatedAt）。
2. 讀 `data/gpt/runs/main/player-state.json`（玩家最新狀態）。
3. 依 `save.json.stage` 讀對應檔案：
   - `01`：`01-副本生成prompt.md` + 最近事件
   - `02`：`02-副本演化prompt.md` + `01-summary.json` + `run-state.json` + 最近 `02-round-*`
   - `03`：`03-現實生成prompt.md` + `02-state.json` + 最近事件
   - `04`：`04-現實演化prompt.md` + `03-summary.json` + `reality-state.json` + 最近 `04-round-*`
4. 讀 `events.jsonl` 最近 20 筆，重建短期記憶。
5. 先輸出「重開摘要」，再開始本輪互動。

### B. 重開摘要格式（固定）

每次新對話第一則正式回覆，先輸出：

1. `目前階段`：`01/02/03/04`
2. `上一輪結論`：上一輪發生了什麼（3-6 行）
3. `玩家目前狀態`：HP/MP/SAN、關鍵資源、紅名/關係壓力
4. `當前目標`：這一輪要推進什麼
5. `可用行動`：2-4 個選項 + 可自由輸入

### C. 防重覆與防漏規則

- 若 `updatedAt` 與最後事件 ID 沒變，不得重跑同一輪。
- 先檢查是否已寫入對應 `xx-round-*` 檔，再決定是否新建下一輪。
- 不得在新對話重置世界狀態；唯一真實來源是 GitHub 檔案。

### D. 給使用者的開新對話固定指令（可直接貼）

```text
請先執行重開機制：
1) 讀 data/gpt/runs/main/save.json 與 player-state.json
2) 依 stage 載入對應 01~04 prompt 與必要 state
3) 讀 events.jsonl 最近 20 筆
4) 先輸出「目前階段 / 上一輪結論 / 玩家目前狀態 / 當前目標 / 可用行動」
完成後再開始本輪演繹。
```

## Git 操作建議

每次完成一輪後，可以提交：

```bash
git add data/gpt/runs/main/
git commit -m "chore(gpt-run): update main save"
```

如果 GPT 沒有直接 commit 權限，就至少把檔案寫進 repo，讓使用者或 agent 後續提交。
