# VCGPT v3 

**VCGPT v3 [(VCGPT_ChatGLM2_Sentiment_Instruction_LoRA_FT)](https://huggingface.co/oliverwang15/VCGPT_ChatGLM2_Sentiment_Instruction_LoRA_FT) is a LLM finetuned with LoRA method on the News and Tweets sentiment analysis dataset which achieve best scores on most of the financial sentiment analysis datasets.**

## Ⅰ. Try our model

``` python
from transformers import AutoModel, AutoTokenizer
from peft import PeftModel

# Load Models
base_model = "THUDM/chatglm2-6b"
peft_model = "oliverwang15/VCGPT_ChatGLM2_Sentiment_Instruction_LoRA_FT"
tokenizer = AutoTokenizer.from_pretrained(base_model, trust_remote_code=True)
model = AutoModel.from_pretrained(base_model, trust_remote_code=True,  device_map = "auto")
model = PeftModel.from_pretrained(model, peft_model)

# Make prompts
prompt = [
'''Instruction: What is the sentiment of this news? Please choose an answer from {negative/neutral/positive}
Input: FINANCING OF ASPOCOMP 'S GROWTH Aspocomp is aggressively pursuing its growth strategy by increasingly focusing on technologically more demanding HDI printed circuit boards PCBs .
Answer: ''',
'''Instruction: What is the sentiment of this news? Please choose an answer from {negative/neutral/positive}
Input: According to Gran , the company has no plans to move all production to Russia , although that is where the company is growing .
Answer: ''',
'''Instruction: What is the sentiment of this news? Please choose an answer from {negative/neutral/positive}
Input: A tinyurl link takes users to a scamming site promising that users can earn thousands of dollars by becoming a Google ( NASDAQ : GOOG ) Cash advertiser .
Answer: ''',
]

# Generate results
tokens = tokenizer(prompt, return_tensors='pt', padding=True, max_length=512)
res = model.generate(**tokens, max_length=512)
res_sentences = [tokenizer.decode(i) for i in res]
out_text = [o.split("Answer: ")[1] for o in res_sentences]

# show results
for sentiment in out_text:
    print(sentiment)

# Output:    
# positive
# neutral
# negative
```

## Ⅱ. Benchmark Results

| ACC/F1 Micro           | BloombergGPT | ChatGLM2 | ChatGLM2 (8-bit) | VCGPT v3 | VCGPT v3 (8-bit) |
| ---------------------- | ------------ | -------- | ---------------- | --------- | ----------------- |
| FPB [1]     | -            | 0.464    | 0.476            | **0.8**   | 0.784             |
| FiQA-SA [2] | -            | 0.822    | **0.833**        | 0.815     | 0.818             |
| TFNS [3] | -            | 0.331    | 0.332            | **0.738** | 0.721             |
| NWGI [4]  | -            | 0.560    | 0.561            | **0.588** | **0.588**         |
| **Macro F1**           |              |          |                  |           |                   |
| FPB [1]  | -            | 0.487    | 0.5              | **0.774** | 0.754             |
| FiQA-SA [2]  | -            | 0.56     | 0.57             | **0.665** | 0.645             |
| TFNS [3]  | -            | 0.34     | 0.34             | **0.681** | 0.652             |
| NWGI [4]  | - | 0.489    | 0.492            | **0.579** | 0.576             |
| **Weighted F1**        |              |          |                  |           |                   |
| FPB [1] | 0.511        | 0.381    | 0.398            | **0.795** | 0.778             |
| FiQA-SA [2]  | 0.751        | 0.79     | 0.801            | **0.806** | 0.801             |
| TFNS [3]  | -            | 0.189    | 0.19             | **0.74**  | 0.721             |
| NWGI [4]  | - | 0.449    | 0.452            | **0.578** | **0.578**         |

[[1] Financial_Phrasebank (FPB) ](https://huggingface.co/datasets/financial_phrasebank) is a financial news sentiment analysis benchmark, the labels are "positive", "negative" and "neutral". We use the same split as BloombergGPT. BloombergGPT only use 5-shots in the test to show their model's outstanding performance without further finetuning. However, is our task, all data in the 'train' part were used in finetuning, So our results are far better than Bloomberg's.

[[2] FiQA SA](https://huggingface.co/datasets/pauri32/fiqa-2018) consists of 17k sentences from microblog headlines and financial news. These labels were changed to "positive", "negative" and "neutral" according to BloombergGPT's paper. We have tried to use the same split as BloombergGPT's paper. However, the amounts of each label can't match exactly when the seed was set to 42.

[[3] Twitter Financial News Sentiment (TFNS)](https://huggingface.co/datasets/zeroshot/twitter-financial-news-sentiment) dataset is an English-language dataset containing an annotated corpus of finance-related tweets. This dataset is used to classify finance-related tweets for their sentiment. The dataset holds 11,932 documents annotated with 3 labels: "Bearish" ("negative"), "Bullish" ("positive"), and "Neutral".

[[4] News With GPT Instruction (MWGI)](https://huggingface.co/datasets/oliverwang15/news_with_gpt_instructions) is a dataset whose labels were generated by ChatGPT. The train set has 16.2k samples and the test set has 4.05k samples. The dataset not only contains 7 classification labels: "strong negative", "moderately negative", "mildly negative", "neutral", "mildly positive", "moderately positive", "strong positive". but it also has the reasons for that result, which might be helpful in the instruction finetuning.


## Ⅲ. How to Train

Coming Soon.
