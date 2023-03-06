# ChatGPT 提示工程

在本節中，我們介紹了 ChatGPT 的最新提示工程技術，包括技巧、應用程序、限制、論文和其他閱讀材料。

**請注意，此部分正在大力開發中。**

主題：
- [ChatGPT 介紹](#chatgpt-介紹)
- [回顧對話任務](#reviewing-the-conversation-task)
- [與 ChatGPT 的對話](#conversations-with-chatgpt)

---
## ChatGPT 簡介

ChatGPT 是一種新模型 [由 OpenAI 訓練](https://openai.com/blog/chatgpt)，能夠以對話方式進行交互。該模型經過訓練，可以按照提示中的說明在對話上下文中提供適當的響應。ChatGPT 可以幫助回答問題、建議食譜、以特定風格編寫歌詞、生成代碼等等。

ChatGPT 使用人類反饋強化學習 (RLHF) 進行訓練。雖然這個模型比以前的 GPT 迭代更強大（並且還經過訓練可以減少有害和不真實的輸出），但它仍然存在局限性。讓我們通過具體示例介紹一些功能和限制。

您可以在 [此處](chat.openai.com) 使用 ChatGPT 的研究預覽，但對於示例，我們將使用 OpenAI Playground 上的聊天模式。

---
## 回顧對話任務

在之前的指南之一中，我們介紹了一些關於對話功能和角色提示的內容。我們介紹瞭如何指導 LLM 以特定的風格、特定的意圖、行為和身份進行對話。

讓我們回顧一下我們之前的基本示例，在該示例中我們創建了一個對話系統，該系統能夠對問題產生更多的技術和科學回答。

*迅速的：*
```
以下是與一位AI研究助理的對話。助理語氣是技術性的、科學性的。

人類：你好，你是誰？
艾：您好！我是一名人工智能研究助理。今天我能幫到你什麼？
人類：你能告訴我黑洞的產生嗎？
人工智能：
```

從上面的示例中，您可以看到兩個重要的組件：
- 聊天機器人的**意圖**或解釋
- 指示聊天機器人將用於響應的樣式或語氣的**身份**

上面的簡單示例適用於使用“text-davinci-003”的文本完成 API。最近，OpenAI [宣布了 ChatGPT API](https://openai.com/blog/introducing-chatgpt-and-whisper-apis)，這是一種更強大、更便宜的模型，稱為“gpt-3.5-turbo”，專門用於專為此類功能（聊天完成）而構建。事實上，OpenAI 推薦這是他們最好的模型，即使對於非聊天用例也是如此。使用 ChatGPT API 的其他好處是顯著降低成本 (90%) 和提高效率。

Snap Inc. 和 Instacart 等大公司已經在其產品中集成了由 ChatGPT 提供支持的對話功能，範圍從個性化推薦到開放式購物目標。

---
## 與 ChatGPT 的對話
為了開始演示 ChatGPT 的功能，我們將使用上面的聊天機器人助手示例並討論結果。與“text-davinci-003”相比，支持 ChatGPT 的“gpt-3.5-turbo”模型使用聊天格式作為輸入。該模型需要一系列消息作為輸入，並使用這些消息生成響應。

*輸入：*
```
SYSTEM：你是一名人工智能研究助理。您使用的是技術和科學的語氣。
用戶：你好，你是誰？
助理：您好！我是一名人工智能研究助理。今天我能幫到你什麼？
用戶：你能告訴我黑洞的產生嗎？
```

*輸出：*
```
助理：當然！當一顆大質量恆星耗盡燃料並在自身引力作用下坍塌時，就會產生黑洞。這種坍縮導致恆星變得異常緻密，引力場強大到連光都無法逃脫。這個不歸路點被稱為事件視界。一旦恆星穿過事件視界，它就被認為是一個黑洞……
```

請注意，在上面的示例中，我簡化了輸入和輸出，但 ChatGPT 聊天完成 API 要求消息採用特定格式。我在下面添加了一個快照，展示了在 OpenAI Playground 中使用“聊天模式”時該示例的外觀：

![](../img/chatgpt-1.png)

我們示例的更正式的 API 調用類似於以下示例：

```蟒蛇
導入openai

openai.ChatCompletion.create(
  型號="gpt-3.5-turbo",
  消息=[
        {"role": "system", "content": "你是一名 AI 研究助理，你用的是技術和科學的語氣。"},
        {"role": "user", "content": "你好，你是誰？"},
        {"role": "assistant", "content": "您好！我是人工智能研究助理，今天有什麼能幫到您嗎？"},
        {"role": "user", "content": "你能告訴我黑洞的產生過程嗎？"}
    ]
)
```
事實上，未來開發者與 ChatGPT 交互的方式有望通過 [Chat Markup Language](https://github.com/openai/openai-python/blob/main/chatml.md) (ChatML for短的）。

聊天格式支持多輪對話，但它也支持單輪任務，類似於我們在“text-davinci-003”中使用的任務。這意味著我們可以使用 ChatGPT 執行與我們為原始 GPT 模型演示的任務類似的任務。例如，讓我們嘗試使用 ChatGPT 執行以下問答任務：

*輸入：*
```
用戶：根據以下上下文回答問題。保持答案簡短明了。如果不確定答案，請回复“Unsure about answer”。

背景：Teplizumab 的根源可以追溯到一家名為 Ortho Pharmaceutical 的新澤西製藥公司。在那裡，科學家們生成了早期版本的抗體，稱為 OKT3。該分子最初來自小鼠，能夠與 T 細胞表面結合併限制其殺傷細胞的潛力。1986 年，它被批准用於幫助預防腎移植後的器官排斥反應，使其成為第一個獲准用於人類的治療性抗體。

問：OKT3最初來源於什麼？

回答：
```

*輸出：*
```
助理：老鼠。
```

請記住，我添加“USER”和“ASSISTANT”標籤是為了更好地演示如何使用 ChatGPT 執行任務。以下是使用 Playground 的示例：

![](../img/chatgpt-classic.png)

更正式地說，這是 API 調用（我只包含了請求的消息部分）：

```蟒蛇
[
  {"role": "user", "content": '根據以下上下文回答問題。保持答案簡短明了。如果不確定答案，請回复“Unsure about answer”。
  
  背景：Teplizumab 的根源可以追溯到一家名為 Ortho Pharmaceutical 的新澤西製藥公司。在那裡，科學家們生成了早期版本的抗體，稱為 OKT3。該分子最初來自小鼠，能夠與 T 細胞表面結合併限制其殺傷細胞的潛力。1986 年，它被批准用於預防腎移植後的器官排斥反應，成為第一個獲准用於人類的治療性抗體
  
  問：OKT3最初來源於什麼？
  
  回答：'}
]
```

---

更多即將推出！



---
＃＃ 參考

- [SemEval-2023 任務 9 中的 UZH_CLyp：用於推文親密預測中跨語言學習的 Head-First Fine-Tuning 和 ChatGPT 數據生成](https://arxiv.org/abs/2303.01194)（2023 年 3 月）
- [ChatGPT 可以評估人的性格嗎？通用評估框架](https://arxiv.org/abs/2303.01248)（2023 年 3 月）
- [通過 ChatGPT 進行跨語言摘要](https://arxiv.org/abs/2302.14229)（2023 年 2 月）
- [ChatGPT 博士，告訴我我想听的：即時知識如何影響健康答案的正確性](https://arxiv.org/abs/2302.13793)（2023 年 2 月）
- [ChatGPT 對數學應用題 (MWP) 的獨立評估](https://arxiv.org/abs/2302.13814)（2023 年 2 月）
- [ChatGPT：2.5 個月後的元分析](https://arxiv.org/abs/2302.13795)（2023 年 2 月）
——【聊聊吧！與 ChatGPT 的對話：技術、應用和局限性](https://arxiv.org/abs/2302.13817)（2023 年 2 月）
- [檢查事實並重試：利用外部知識和自動反饋改進大型語言模型](https://arxiv.org/abs/2302.12813)（2023 年 2 月）
- [關於 ChatGPT 的穩健性：對抗性和分佈外的觀點](https://arxiv.org/abs/2302.12095)（2023 年 2 月）
- [如何在 SPC 實踐、教育和研究中（錯誤地）使用 ChatGPT 等生成式 AI 模型？探索性研究](https://arxiv.org/abs/2302.10916)（2023 年 2 月）
- 【ChatGPT 也能聽懂嗎？ChatGPT 和微調 BERT 的比較研究](https://arxiv.org/abs/2302.10198)（2023 年 2 月）
- [使用 ChatGPT 增強提示工程的提示模式目錄](https://arxiv.org/abs/2302.11382)（2023 年 2 月）
- [通過與 ChatGPT 聊天進行零樣本信息提取](https://arxiv.org/abs/2302.10205)（2023 年 2 月）
- [ChatGPT：萬事通，萬事通](https://arxiv.org/abs/2302.10724)（2023 年 2 月）
- [ChatGPT 和 DALL-E 2 關於決策和空間推理的試點評估](https://arxiv.org/abs/2302.09068)（2023 年 2 月）
- [網民、院士和信息專業人士對 AI 的看法，特別參考 ChatGPT](https://arxiv.org/abs/2302.07136)（2023 年 2 月）
- [ChatGPT 中的語言歧義分析](https://arxiv.org/abs/2302.06426)（2023 年 2 月）
- [ChatGPT 與知識圖的傳統問答：知識圖聊天機器人的現狀和未來方向](https://arxiv.org/abs/2302.06466)（2023 年 2 月）
- [ChatGPT 和生成式人工智能對科學意味著什麼](https://www.nature.com/articles/d41586-023-00340-6)（2023 年 2 月）
- [將 BERT 和 ChatGPT 應用於科學文獻中萊姆病的情感分析](https://arxiv.org/abs/2302.06474)（2023 年 2 月）
- [ChatGPT 好嗎？關於大型教育語言模型的機遇和挑戰](https://www.edu.sot.tum.de/fileadmin/w00bed/hctl/_my_direct_uploads/ChatGPT_for_Good_.pdf)（2023 年 1 月）
- [提高可靠性的技術 - OpenAI Cookbook](https://github.com/openai/openai-cookbook/blob/main/techniques_to_improve_reliability.md)
- [很棒的 ChatGPT 提示](https://github.com/f/awesome-chatgpt-prompts)
- [ChatGPT 簡介](https://openai.com/blog/chatgpt)（2022 年 11 月）

---
[上一節（應用程序）](./prompts-applications.md)

[下一節（對抗性提示）](./prompts-adversarial.md)