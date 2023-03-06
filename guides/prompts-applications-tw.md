# 提示應用程序

在本指南中，我們將介紹一些高級和有趣的方法，我們可以使用提示工程來執行有用和更高級的任務。

**請注意，此部分正在大力開發中。**
主題：
- [生成數據](#generating-data)
- [作為推理的代碼](#pal-program-aided-language-models-code-as-reasoning)
- ...


---
## 生成數據
LLM 具有強大的文本生成能力。使用有效的提示策略可以引導模型產生更好、一致和更符合事實的響應。LLM 還可以特別用於生成對運行各種實驗非常有用的數據。例如，我們可以使用它為情感分類器生成快速樣本，如下所示：

*迅速的：*
```
製作 10 個用於情緒分析的範例。示例被歸類為正面或負面。產生 2 個反例和 8 個正例。示例使用此格式：
問：<句子>
答：<情緒>
```

*輸出：*
```
問：我剛剛得到最好的消息！
答：正面

問：我們剛加薪！
答：正面

問：我為今天所取得的成就感到非常自豪。
答：正面

問：我度過了最美好的一天！
答：正面

問：我真的很期待週末。
答：正面

問：我剛剛收到最好的禮物！
答：正面

問：我現在很開心。
答：正面

問：我很幸運有這樣一個很棒的家庭。
答：正面

問：外面的天氣很陰沉。
答：否定

問：我剛得到一些可怕的消息。
答：否定
```

這非常有用。實際上，我們將此示例用於指南另一部分中的不同測試。

---

## PAL（程序輔助語言模型）：代碼推理
 
[Gao et al., (2022)](https://arxiv.org/abs/2211.10435) 提出了一種使用 LLM 閱讀自然語言問題並生成程序作為中間推理步驟的方法。創造的程序輔助語言模型 (PAL)，它與思維鏈提示的不同之處在於，它不是使用自由格式的文本來獲取解決方案，而是將解決方案步驟卸載到編程運行時，例如 Python 解釋器。

![](../img/pal.png)

讓我們看一個使用 LangChain 和 OpenAI GPT-3 的例子。我們有興趣開發一個簡單的應用程序，該應用程序能夠解釋所提出的問題並通過利用 Python 解釋器提供答案。

具體來說，我們有興趣創建一個功能，允許使用 LLM 來回答需要理解日期的問題。我們將為 LLM 提供一個提示，其中包括從 [此處](https://github.com/reasoning-machines/pal/blob/main/pal/prompt/date_understanding_prompt.py) 採用的一些範例。  

這些是我們需要的導入：

```蟒蛇
導入openai
從日期時間導入日期時間
從 dateutil.relativedelta 導入 relativedelta
導入操作系統
從 langchain.llms 導入 OpenAI
從 dotenv 導入 load_dotenv
```

讓我們首先配置一些東西：

```蟒蛇
load_dotenv()

# 接口配置
openai.api_key = os.getenv("OPENAI_API_KEY")

# 對於朗鏈
os.environ["OPENAI_API_KEY"] = os.getenv("OPENAI_API_KEY")
```

設置模型實例：

```蟒蛇
llm = OpenAI(model_name='text-davinci-003', temperature=0)
```

設置提示+問題：

```蟒蛇
question = "今天是 2023 年 2 月 27 日。我剛好 25 年前出生。我出生的日期是 MM/DD/YYYY？"

DATE_UNDERSTANDING_PROMPT = """
# Q：還有36個小時2015年就要到了。MM/DD/YYYY 從今天算起一周後的日期是幾號？
# 如果 2015 年在 36 小時後到來，那麼今天是 36 小時前。
今天 = datetime(2015, 1, 1) - relativedelta(hours=36)
# 從今天開始一周，
one_week_from_today = 今天 + relativedelta(weeks=1)
# 格式為 %m/%d/%Y 的答案是
one_week_from_today.strftime('%m/%d/%Y')
# Q: 2019年的第一天是星期二，今天是2019年的第一個星期一，MM/DD/YYYY今天是幾號？
# 如果2019年的第一天是星期二，今天是2019年的第一個星期一，那麼今天就是6天后。
今天 = datetime(2019, 1, 1) + relativedelta(days=6)
# 格式為 %m/%d/%Y 的答案是
今天.strftime('%m/%d/%Y')
# Q: 原定06/01/1943的演唱會推遲一天到今天。10 天前的日期是 MM/DD/YYYY？
# 如果音樂會原定於 06/01/1943 舉行，但推遲了一天到今天，那麼今天就是晚一天。
今天 = datetime(1943, 6, 1) + relativedelta(days=1)
#10天前，
十天前 = 今天 - 相對增量（天數 = 10）
# 格式為 %m/%d/%Y 的答案是
ten_days_ago.strftime('%m/%d/%Y')
# 問：今天是 4/19/1969。24 小時後的日期是 MM/DD/YYYY？
# 今天是 4/19/1969。
今天 = 日期時間 (1969, 4, 19)
#24小時後，
後來=今天+相對增量（小時=24）
# 格式為 %m/%d/%Y 的答案是
今天.strftime('%m/%d/%Y')
# 問：Jane 認為今天是 3/11/2002，但實際上今天是 3 月 12 日，也就是 1 天之後。24 小時後的日期是 MM/DD/YYYY？
# 如果 Jane 認為今天是 2002 年 3 月 11 日，但實際上今天是 3 月 12 日，那麼今天是 2002 年 3 月 1 日。
今天 = 日期時間 (2002, 3, 12)
#24小時後，
後來=今天+相對增量（小時=24）
# 格式為 %m/%d/%Y 的答案是
later.strftime('%m/%d/%Y')
#Q：Jane出生於2001年二月的最後一天，今天是她16歲的生日。MM/DD/YYYY 昨天是幾號？
# 如果 Jane 出生於 2001 年 2 月的最後一天，今天是她 16 歲的生日，那麼今天是 16 年後。
今天 = datetime(2001, 2, 28) + relativedelta(years=16)
＃ 昨天，
昨天 = 今天 - 相對增量（天 = 1）
# 格式為 %m/%d/%Y 的答案是
昨天.strftime('%m/%d/%Y')
# 問：{問題}
"".strip() + '\n'
```

```蟒蛇
llm_out = llm（DATE_UNDERSTANDING_PROMPT.format（問題=問題））
打印（llm_out）
```

```蟒蛇
執行（llm_out）
打印（出生）
```

這將輸出以下內容：`02/27/1998`

查看完整筆記本 [此處](../notebooks/pe-pal.ipynb)

---

更多示例即將推出！

[上一節（高級用法）](./prompts-advanced-usage.md)

[下一節（對抗性提示）](./prompt-adversarial.md)