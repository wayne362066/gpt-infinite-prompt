# Party Infinite GPT Runtime Notes

此資料夾為網頁 GPT + GitHub 存讀版的實際遊玩資料區。

固定存檔根目錄：

```text
data/gpt/runs/main/
```

執行規則：

1. 每次回合開始前先讀取 `save.json` 判斷目前 stage。
2. 依 `save.json.stage` 載入根目錄對應 prompt：`01`、`02`、`03`、`04`。
3. 若 `data/gpt/runs/main/` 沒有 `save.json` 或 `player-state.json`，視為新存檔。
4. 每輪結果必須寫回 `data/gpt/runs/main/`。
5. Markdown 記錄給玩家閱讀；JSON state 給後續回合讀取。
6. 不得只把進度留在聊天上下文。

目前遊玩存檔：`runs/main/`
