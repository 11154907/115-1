# AI 學習筆記整理助手（老師 JSON Schema Starter 改造版）

## 1. 專題目的

本專題將課堂筆記整理流程交給生成式 AI 協助。使用者貼上課堂筆記後，系統透過 OpenAI Responses API 的 Structured Outputs（JSON Schema、`strict: true`）產生固定欄位，方便複習與檢查。

本專題不是一般聊天機器人，也不是只要求模型「用 JSON 回答」；程式會把 JSON Schema 實際傳入 API，並在收到回應後再次檢查欄位與型別。

## 2. 使用者、問題與輸入輸出

- **目標使用者：** 想將課堂筆記整理成複習資料的學生。
- **使用情境：** 課後將上課筆記或教材文字貼入系統，快速取得摘要、重點與自我測驗問題。
- **輸入：** 一段最多 6000 字元的課堂筆記文字。
- **輸出：**
  - `title`：筆記主題
  - `summary`：課堂摘要
  - `key_points`：重要知識點清單
  - `study_questions`：複習問題清單
  - `missing_information`：資訊不足或待補充項目

模型只能依照輸入原文整理，不得自行補造未提供的事實。原文不足時，應在 `missing_information` 說明，並允許清單為空。

## 3. 老師雛型改造說明

本專題以老師提供的 `week08_midterm_simple_starter` 為基礎，保留其主要程式流程與檢查方式，完成 A／B／C 三個個人設計區：

- **A｜APP_TITLE、INSTRUCTIONS：** 設定「AI 學習筆記整理助手」名稱，以及忠於原文、繁體中文、資訊不足處理等任務規則。
- **B｜SCHEMA：** 定義五個欄位，使用 `string` 與 `array of string`，所有欄位列入 `required`，並設定 `additionalProperties: false`。
- **C｜FIELD_LABELS：** 將 Schema 欄位對應成使用者看得懂的繁體中文標籤。

保留老師雛型中的 `read_setting()`、`check_result()`、API 開關、輸入檢查、Structured Outputs 呼叫、拒答／未完成檢查、成功後才保存與顯示等設計。

## 4. 安裝與本機啟動

建議依課程環境使用 Python 3.14；若你的電腦或部署平台使用其他版本，請先確認套件相容性。

Windows PowerShell：

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
Copy-Item .env.example .env
```

macOS / Linux：

```bash
python3.14 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
cp .env.example .env
```

在 `.env` 填入自己的 API 金鑰：

```text
OPENAI_API_KEY=你的金鑰
OPENAI_MODEL=gpt-5.4-mini
```

啟動：

```bash
python -m streamlit run app.py
```

程式會透過 `python-dotenv` 讀取本專案資料夾內的 `.env`。不要將真實金鑰提交到 GitHub。

## 5. 操作方式與 API 安全

1. 開啟 App，API 開關預設關閉。
2. 貼上課堂筆記，按「整理學習筆記」。
3. API 關閉時只會檢查輸入與設計，不會產生 AI 結果，也不會呼叫 API。
4. 確認已設定 API 金鑰與模型後，才勾選「啟用真實 API（可能產生費用）」。
5. 有效提交最多送出一次 API 請求；SDK 設定 `max_retries=0`，不自動重試。
6. 空白輸入、超過 6000 字元、未完成設計或缺少金鑰時，不會進入 API 呼叫。
7. 只有回應完成、非空、JSON 可解析且欄位檢查通過後，才會保存並顯示成功結果。
8. 失敗時顯示錯誤，不會把部分或未驗證內容當成成功結果。

注意：API 開關開啟並成功提交可能產生費用；`store=False` 不代表資料不會傳送到 API，也不代表免計費。請勿輸入個資、機密或未授權內容。

## 6. 測試紀錄

請在實際操作後填寫，不可將範例當成已完成的真實 API 測試。

### 測試 A：正常筆記

- 測試日期：
- 使用模型：
- API 模式：開啟
- 輸入原文：
- 實際輸出 JSON：
- 請求次數：1
- 人工核對：
  - [ ] title 是否符合原文
  - [ ] summary 是否忠於原文
  - [ ] key_points 是否有根據
  - [ ] study_questions 是否能由原文複習
  - [ ] missing_information 是否合理
- 測試結果：

### 測試 B：資訊不足筆記

- 測試日期：
- 使用模型：
- API 模式：開啟
- 輸入原文：
- 實際輸出 JSON：
- 請求次數：1
- 人工核對：
  - [ ] 是否避免猜測或補造資料
  - [ ] 是否清楚指出資訊不足
  - [ ] 是否沒有產生無根據的複習問題
- 測試結果：

### 本機／雲端驗證

- 本機 Python 版本：
- 本機測試結果：
- Streamlit Community Cloud App URL：
- Cloud Python 版本：
- 雲端真實 API 成功測試日期：
- 教師是否可開啟：
- 尚未完成或失敗項目：

## 7. Streamlit Community Cloud 部署

1. 將本專案資料夾內容推送到自己的 GitHub repository。
2. 在 Streamlit Community Cloud 建立 App，選擇 repository、branch。
3. Main file path 設為 `app.py`（如果此資料夾本身就是 repository 根目錄）。
4. 在 App 的 Secrets 設定加入：

```toml
OPENAI_API_KEY = "你的金鑰"
OPENAI_MODEL = "gpt-5.4-mini"
```

5. 部署後先用 API 關閉模式確認頁面能開啟，再設定 Secrets。
6. 在雲端完成至少一筆真實 API 成功測試，並把 App URL 與測試紀錄補到本 README。
7. 不要把 `.env` 或 `secrets.toml` 推送到 GitHub。

## 8. 專案檔案

- `app.py`：Streamlit 主程式與 JSON Schema。
- `requirements.txt`：Python 套件版本。
- `.env.example`：本機金鑰設定範例，不含真實金鑰。
- `.streamlit/secrets.example.toml`：雲端 Secrets 格式範例。
- `sample_data/sample_notes.txt`：可用於測試的虛構課堂筆記。
- `第08週_專題設計與驗收紀錄.md`：專題設計、測試與驗收待填紀錄。

## 9. 限制與後續改進

- 目前只接受純文字輸入，最多 6000 字元。
- 目前只支援 Schema 中的 `string` 與 `array of string`。
- JSON Schema 與型別檢查只能確保輸出結構符合規格，不能保證摘要內容完全正確；仍需對照原文人工覆核。
- 模型可能漏掉重點、誤解上下文或提出不理想的複習問題。
- 可再加入筆記分類、匯出檔案或不同科目模板，但不應取代本次 Structured Outputs 核心要求。
