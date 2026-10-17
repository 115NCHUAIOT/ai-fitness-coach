# AI Fitness Coach：以對話為入口的智慧慢跑教練系統

## Abstract

**A. Attention Getter 動機及引題**  
隨著全民運動意識提升與 AIoT、大型語言模型技術快速發展，個人化運動指導需求日增。使用者期望能像與真人教練對話般，快速取得結合天氣、路線、個人狀態與飲食的慢跑建議，而非填寫多頁表單。

**B. But (However) 挑戰**  
現有健身應用多以記錄為主，缺乏即時對話互動、環境感知整合與具臨場感的數位人像。同時面臨語音延遲、多模組整合複雜、免費雲端資源限制，以及健康相關資料的用量與隱私控制等挑戰。

**C. Cure 解藥**  
我們提出 AI Fitness Coach——以對話為唯一入口的慢跑教練網站。整合語音 Agent、數位人像口型、天氣路線、食物視覺辨識與營養建議，讓使用者進入首頁即可自然互動並獲得可執行計畫。

**D. Development 方法設計**  
本方法基於 CyborAgent（語音對話與工具迴圈）與 CyborAssistant（數位人像與口型）整併至單一主倉，採用 Vanilla JS + FastAPI 架構，部署於 Vercel + Supabase，並以單一 FitnessCoachAgent 搭配嚴格 ScopeGuard 與用量預算控制成本。

**E. Experiments**  
為評估所提方案，我們進行端到端流程驗證，包含對話建議、天氣與路線查詢、手動運動紀錄、食物熱量辨識、MCP 營養菜單，以及數位人像口型同步展示。

**F. Findings**  
結果顯示，系統可在免費優先的雲端環境下完成主要使用者流程，並透過工具迴圈與 ScopeGuard 有效控制模型用量，為 AIoT 專題提供可公開展示的整合架構。

---

## I. Introduction

**G. General 現況**  
隨著穿戴裝置與 AI 教練普及，數位健身市場快速成長，相關技術在運動記錄與簡單建議上已有顯著進展。

**H. However 挑戰**  
然而仍存在若干挑戰：多數系統需使用者先填寫表單，缺乏自然對話入口；環境資訊（天氣、路線）、飲食與即時語音人像難以整合；且在免費雲端資源下難以同時滿足延遲、成本與可部署性。

**I. In literature**  
文獻中有關此問題的解決方法可大致分為傳統表單式健身 App、純語音助理、姿勢偵測系統，以及近年的 LLM Agent 與即時語音口型方案，從舊式記錄工具到較複雜的多模態系統。

**J. Judgement**  
評論各方法優缺點：表單式易於實作但互動生硬；語音助理自然但缺乏視覺臨場感與環境工具；完整數位人系統成本高且整合困難。本方案以單一 Agent + 工具迴圈在免費優先前提下取得平衡。

**K. Keypoint**  
本文主要目標與貢獻為：(1) 建立以對話為入口的慢跑教練系統；(2) 整合天氣、路線、飲食與數位人像；(3) 提供可公開連線的雲端部署；(4) 實作 ScopeGuard 與用量預算機制。

**L. List the organization**  
本文結構如下：第 II 節相關研究、第 III 節提出方法、第 IV 節模擬與驗證、第 V 節結論。

---

## II. Related Work

**M. Methods**  
文獻中有關此問題的解決方法，大分類從舊到新、簡單到複雜：傳統健身記錄 App → 語音助理型 → 姿勢／感測分析 → LLM Agent + 工具呼叫 → 即時語音與唇型同步數位人。

**N. New proposed**  
近年新趨勢包含 LLM 工具迴圈（function calling）、MCP 協議、低延遲 STT/TTS，以及可嵌入的數位人像口型技術。本方案的 novelty 在於將上述能力整合成「對話入口 + 嚴格範圍控制 + 免費雲端可部署」的完整慢跑教練流程。

**O. Organize**  
基於 Judgement 評述，依優缺點歸納比較（可進一步製成表格）：互動自然度、環境感知能力、飲食整合、部署成本、數位人像臨場感、用量可控性。

---

## III. Proposed Scheme (From PA to PM)

**PA. Aim = Statement 目標**  
開發可公開連線的 AI 慢跑教練網站，使用者能以文字或語音詢問「今天怎麼跑」，系統結合實際工具資料提出可採用計畫，並支援飲食分析與紀錄查詢。

**PB. Based on 背景／基礎**  
基於 CyborAgent 的聊天／語音／Agent 能力與 CyborAssistant 的人像／口型佇列，整併至主倉 `ai-fitness-coach`，並擴充慢跑業務邏輯（計畫、紀錄、飲食）。

**PC. Cure / Cause 解法與原因**  
採用單一 FitnessCoachAgent + 非 LLM 意圖篩選（ScopeGuard）+ 有型別工具迴圈，原因是避免多 Agent 框架複雜度與成本失控；後端先驗證再呼叫模型，無關請求不啟動 LLM／TTS／口型。

**PD. Design 架構**  
- 前端：HTML / CSS / Vanilla JavaScript ES modules  
- 後端：Python FastAPI（Vercel Functions）  
- 資料：開發 SQLite、正式 Supabase Postgres  
- 即時：WebSocket 傳送對話與語音事件  
- 模型：LLM / STT / TTS / Vision 各設 provider adapter  
- 口型：獨立服務，與主站分離 GPU 依賴  

**PE. Paper element 重要元素**  
關鍵元素包含：教練對話與 Agent、天氣定位路線、食物熱量辨識、營養菜單 MCP、AI 人像與語音、運動紀錄與個人設定。

**PF. Formulation / Foundation**  
包含距離／配速計算規則、營養換算與 snapshot 保存、用量預算原子更新、冪等鍵與狀態機設計，確保資料一致性與可重現性。

**PG. Graph 架構圖說明**  
系統由主前端、主後端、Coach Agent、工具層（天氣、路線、飲食、MCP）與獨立口型服務組成，對話與語音事件經 WebSocket 統一處理。

**PH. How**  
使用者輸入 → ScopeGuard 判斷範圍 → 產生 TaskSpec → Agent 呼叫允許工具 → 後端驗證結果與 schema → 產生文字／卡片／TTS → 口型同步播放。

**PI. Implementation / Interaction among elements**  
REST 負責業務資料保存（計畫、運動、飲食），WebSocket 負責即時對話與語音事件。所有模型呼叫共用預算，工具結果帶 source 與有效時間。

**PJ. Jump to an example**  
範例：「今天想跑 30 分鐘」→ 取得天氣與路線 → 產生計畫草案 → 使用者確認「採用並開始慢跑」→ 建立 active run → 完成後手動輸入距離並保存。

**PK. Key points contribution**  
主要貢獻為對話入口整合、嚴格範圍與用量控制、可公開雲端部署，以及模組化整併策略。

**PL. Later how to do simulation**  
第一階段以手動距離與展示帳號驗證完整流程；第二階段再擴充持續 GPS 與感測。

**PM. Math proof**  
（如適用）配速 = 有效運動秒數 ÷ 公里數；營養依標準基準量換算並保存 snapshot，避免資料庫更新影響歷史紀錄。

---

## IV. Simulation

**Q. Quality and Quantity**  
指標包含：一般頁面查詢 P95 < 2 秒、文字至首段有效回覆 P95 < 8 秒、語音至可聽回覆 P95 < 12 秒、停止播放 < 300 ms。功能 smoke test 至少 10 回合。

**R. Related method to compare**  
與純表單式健身 App、無口型語音助理、以及本地部署方案比較互動體驗、部署便利性與成本。

**S. Simulation Setup**  
預建展示帳號與資料隔離；展示區域路線人工核對；M0 驗證模型供應者免費額度、中文 STT/TTS 與端到端延遲。

**T. Tuning what parameters**  
調整工具迭代上限、單回合 token 預算、音訊長度限制、口型逾時門檻與每日模型配額。

**U. Useful results**  
展示完整腳本：登入 → 查詢計畫與天氣路線 → 人像解說與插話 → 採用並開始慢跑 → 飲食上傳與修正 → 紀錄查回。

**V. Verify the effectiveness**  
透過單元／整合測試、模組契約測試、範圍與成本測試（含拒絕無關請求）、真機瀏覽器測試與外部服務 smoke test 驗證有效性。

---

## V. Conclusion

**W. What proposed**  
回顧研究意義與動機：提出以對話為入口、整合環境感知與數位人像的 AI 慢跑教練系統，解決現有應用互動不足的問題。

**X. eXcel**  
強調方法與解決方案優勢：單一 Agent + ScopeGuard 降低複雜度與成本，免費優先雲端架構提升可部署性，模組化整併便於維護。

**Y. Yields**  
實驗與結果簡述：第一階段可完成端到端主要流程並公開展示，用量控制機制有效避免超額。

**Z. Zen**  
未來工作：第二階段擴充手機瀏覽器持續 GPS 追蹤、計步與姿態感測；優化口型雲端成本；探索長期個人化記憶。
