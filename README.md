# make-knowledge-cards

將貼上的文章或本地 TXT 轉成知識卡片的 Codex Skill。這是一個由 Codex 執行的指令型技能，沒有獨立應用程式或圖形介面。

## 解決什麼問題

文章中常有重複敘述、枝節與分散的重點，直接拿來複習不容易。本技能將重要知識整理成可獨立閱讀的卡片，保留原文的條件與限制，方便理解和自測。

## 主要功能

- 接受對話中貼上的文章，或使用者指定的本地 `.txt` 檔案。
- 提取重要知識、合併重複觀點，一張卡片只講一個知識點。
- 成功交付時固定輸出 9 張卡片；超過 9 個重要知識點時選取最重要的 9 個。去重後不足 9 個時說明數量並請求補充原文，暫不交付卡片，不編造或拆碎概念湊數。
- 每張卡片包含標題、核心知識、簡明解釋，以及原文例子或自測問題。原文沒有例子時使用自測問題，不編造案例。
- 保留來源的適用範圍、數值與不確定性，不補充原文沒有的資訊。
- 預設在對話中交付 Markdown；使用者要求保存時才寫入指定位置。

目前不支援網頁抓取、PDF 或圖形介面操作。技能也不負責查證原文事實或補充外部資料。

## 安裝方法

需要可使用本地 Skills 的 Codex 環境；讀取 TXT 或保存結果時，Codex 必須具有相應檔案的存取權限。技能本身不需要安裝 Python 套件、額外腳本或連接外部服務；模型執行仍依賴你的 Codex 環境。

本專案的技能原始檔位於 `Skill/make-knowledge-cards/`。請複製整個資料夾，包含 `SKILL.md` 和 `agents/openai.yaml`，至以下其中一個位置：

- 專案內使用：`<目標專案>/.agents/skills/make-knowledge-cards/`，並在該專案目錄啟動 Codex。
- 個人使用：`~/.agents/skills/make-knowledge-cards/`；Windows 中 `~` 指使用者資料夾。

例如，在本專案根目錄用 PowerShell 安裝至本專案：

```powershell
New-Item -ItemType Directory -Path '.agents/skills' -Force | Out-Null
Copy-Item -LiteralPath 'Skill/make-knowledge-cards' -Destination '.agents/skills/make-knowledge-cards' -Recurse
```

以上適用於首次安裝；目的地已有同名技能時，先核對內容再更新。安裝後確認技能可被選取；若未出現，重新啟動 Codex。安裝目錄依據 [OpenAI 官方 Skills 文件](https://learn.chatgpt.com/docs/build-skills)。

`Skill/` 是本專案的開發位置，不會因為放在此處就自動安裝。這份 README 提供安裝步驟，不代表專案已執行安裝。

## 使用方法

確認環境已識別技能後，在對話中指定技能並貼上文章：

```text
請使用 $make-knowledge-cards，將以下文章整理成知識卡片：
（貼上文章）
```

或提供本地 TXT 路徑：

```text
請使用 $make-knowledge-cards，讀取本地 TXT「C:/notes/article.txt」，整理成知識卡片。
```

需要保存時，可明確指定位置：

```text
請使用 $make-knowledge-cards，讀取本地 TXT「C:/notes/article.txt」，
將知識卡片保存至「C:/notes/cards.md」。
```

範例路徑請換成實際可讀寫的位置。若檔案讀取失敗、出現亂碼或內容不完整，技能會說明問題並要求補充，不會猜測缺失內容。

## 輸入與輸出示例

以下短文示範資訊不足時的回應；完整 9 張卡片的輸入與輸出見 [測試輸入案例 G](tests/inputs.txt) 與 [測試結果案例 G](tests/results.md)。

輸入：

```text
會議開始前應明確列出要討論的議題。開會前要列清楚討論議題。
會議結束時應記錄每項決定的負責人。每個決定都要記下由誰負責。
```

輸出：

```markdown
去除同義重複後，目前只能提取 2 個重要且不同的知識點：會前列出議題、會後記錄決定負責人。
不足以製作固定 9 張卡片，請補充包含更多重要知識的原文；目前暫不生成卡片。
```

## 本地驗證

`tests/inputs.txt` 包含自撰的技術說明、研究摘要、重複短文、敘事及邊界案例，並涵蓋不足 9 個、剛好 9 個與超過 9 個知識點；`tests/results.md` 保存按技能執行的輸出與人工核對記錄。這些是建立者的人工行為測試，不是獨立模型或自動化測試。

使用官方 skill-creator 所附的工具檢查技能格式：

```text
python -X utf8 <skill-creator 路徑>/scripts/quick_validate.py Skill/make-knowledge-cards
```

技能指令不保證模型永遠正確；需使用的卡片仍應與原文核對。

## 授權

MIT License，見 [LICENSE](LICENSE)。
