[# HW1 submission

**Name: Zhidekhan Magzhan**
**Student ID: S23067686**
**Group: cs4007**
**Repository: https://github.com/magzhanzhidekhan/hw1-magzhanzhidekhan**

## AI tool disclosure

State which AI tools you used and for what. Expected and fine; undisclosed use
is not.

> I used Claude as an assistant throughout the entire completion of 
> this task. Specifically, the AI helped me: explain the mechanics of how 
> the OpenAI/OpenRouter API works and write code when implementing the functions. 
> The AI also helped me find and fix errors during the process. And he helped me 
> structure and format the written analysis based on the numbers obtained when running the code.

---

## Sublab Easy — the registration bot and its bill

**How I laid the catalogue out inside the system prompt, and why:**

>I listed each course from the catalog as a separate line in the following 
> format: code: name | credits | prerequisites | schedule | seats (occupied/total, remaining) | instructor. 
> This line‑by‑line format is easy for the model to read line by line and doesn’t require it to parse the 
> nested JSON on its own. Separately, I included a list of courses the student has already completed, the 
> minimum and maximum credit limit per semester, and instructions to decline any course that isn’t 
> in the catalog instead of inventing one.

**My turn 5 (Kazakh or Russian):**

>Мен үшінші курс студентімін. Мен әлі де қандай курстарға жазыла аламын?

### Run 1 — OpenAI, `gpt-5.6-luna`

| Turn | Input tokens | Output tokens | Cost $   |
|---|------------|-------------|----------|
| 1 | 685        | 331         | 0.000534 |
| 2 | 898        | 118         | 0.000321 |
| 3 | 985        | 74          | 0.000286 |
| 4 | 1078       | 36          | 0.000259 |
| 5 | 1142       | 474         | 0.000797 |
| **total** |            |             | 0.002197 |

### Run 2 — OpenRouter, `google/gemma-3-27b-it`

| Turn | Input tokens | Output tokens | Cost $   |
|---|--------------|---------------|----------|
| 1 | 791          | 359           | 0.000121 |
| 2 | 1175         | 172           | 0.000122 |
| 3 | 1370         | 198           | 0.000141 |
| 4 | 1589         | 60            | 0.000137 |
| 5 | 1681         | 384           | 0.000196 |
| **total** |              |               | 0.000716 |

### Turn 4, verbatim

The turn where you asked for CSS-4090, which does not exist. Paste both replies
exactly as they came back — do not tidy them.

**OpenAI:**

```
bot: I cannot add **CSS-4090 — Quantum Machine Learning** because it does not exist in the **2026-FALL Narxoz University course catalogue**.
     in=  1084  out=   36  $0.000260
```

**OpenRouter:**

```
bot: I am sorry, but **CSS-4090 Quantum Machine Learning does not exist** in the course catalogue for the Fall 2026 term. I am only able to register you for courses listed in the available catalogue. Please choose a course from the list provided previously.
     in=  1589  out=   60  $0.000137
```

### Written answers

**1. The two providers used almost identical code. What actually changed, and
what did not?**

>What has changed: only the client’s base_url (https://openrouter.ai/api/v1 instead of the default 
> OpenAI address) and the model name passed to chat.completions.create (gpt-5.6-luna vs. google/gemma-3-27b-it). 
> What hasn’t changed: the client class itself (OpenAI from the openai library), the message format 
> (messages — a list of dictionaries with roles system/user/assistant), and the method for obtaining 
> the response text. (response.choices[0].message.content) and the way to read the number of tokens 
> (response.usage.prompt_tokens / completion_tokens). This confirms the main idea of the task: OpenRouter 
> uses the same “wire” format (wire format) as OpenAI, so the same client code, written once, 
> serves both providers — only the connection configuration changes, not the logic.

**2. Why did the input token count climb on every turn when your questions
stayed roughly the same length? Use the numbers from your own table. What
happens to the bill at fifty turns?**

>Input tokens increased from 791 on turn 1 to 1681 on turn 5 — more than twice as much, even though my 
> questions remained roughly the same length. The thing is that the model doesn’t remember anything 
> between calls. The run_turn function sends the entire dialogue anew each time: the system prompt + 
> all previous questions + all previous bot responses + the new question. Therefore, input tokens are 
> not the length of the current question, but the length of the entire history, which only keeps growing.

**3. Turn 4: did the bot refuse, or did it invent CSS-4090?** If it refused, what
in your system prompt held the line? If it invented, what did it make up —
credits, a room, an instructor?

>Both bots refused to register CSS-4090 and didn’t make anything up. I believe the 
> decisive factor was the instruction in the system prompt: “You know ONLY what is listed below... 
> refuse to register it and say so plainly. Do not invent courses, credits, instructors, or seat 
> counts under any circumstances.”

**4. Where else was either bot wrong?** Turn 2 asks for two courses that meet at
the same hour; two courses in the catalogue are full. Did the bots notice?

> Both bots correctly detected the scheduling conflict between CSS-4007 and CSS-4102, 
> as well as the fact that CSS-4400 is filled.

---

## Sublab Medium — one task, six models

| Model | Exact | Failed | Tokens | Cost $ |
|---|---|---|---|---|
| google/gemma-3-27b-it | 6 | 0 | 1875 | 0.00020 |
| qwen/qwen3.8-27b | 8 | 0 | 40359 | 0.12471 |
| deepseek/deepseek-v4-flash-0731 | 8 | 0 | 21702 | 0.00585 |
| gpt-5.6-luna | 5 | 0 | 4311 | 0.00402 |
| gpt-5.6-terra | 0 | 8 | 0 | 0.00000 |
| gpt-5.6-sol | 0 | 8 | 0 | 0.00000 |

*(Note: `gpt-5.6-terra` and `gpt-5.6-sol` did not run — the OpenAI account
balance was exhausted after the `gpt-5.6-luna` calls, so these two more
expensive models received no credits at all. `google/gemma-4-26b-a4b-it:free`
was replaced with the paid `google/gemma-3-27b-it` due to a persistent rate
limit on the free shared pool, as noted in the AI tool disclosure.)*

### Which error types did each model repair?

| Error type | gemma | qwen | deepseek | luna | terra | sol |
|---|---|---|---|---|---|---|
| kaz_to_rus | partial | yes | yes | partial | no (failed) | no (failed) |
| latin_homoglyph | yes | yes | yes | yes | no (failed) | no (failed) |
| drop_hyphen | no | yes | yes | yes | no (failed) | no (failed) |
| join_words | partial | yes | yes | partial | no (failed) | no (failed) |
| double_letter | yes | yes | yes | yes | no (failed) | no (failed) |

**The `latin_homoglyph` row: what happened?**

> All four models that actually completed the run fixed both `latin_homoglyph`
> rows (KZ-03 and KZ-08) exactly, matching the published original word for
> word. This held even for `gemma-3-27b-it`, the cheapest model in the whole
> comparison, and even in KZ-08 where the homoglyph was combined with a
> doubled letter in the same word. None of the four struggled with this error
> type at all — every one of them correctly swapped the Latin lookalikes back
> to Cyrillic.

**Where a model returned good Kazakh that was not identical to the original,
say so here.**

> `gpt-5.6-luna`, KZ-01: the model wrote "елшілерінен" (plural, "from
> ambassadors") instead of the published "елшісінен" (singular, "from an
> ambassador") — both are grammatically valid Kazakh, just a different choice
> of grammatical number. `char_diff: 33`.
>
> `gpt-5.6-luna`, KZ-04 and KZ-05: the model added a closing question mark to
> both sentences, which the original dataset sentences lack even though they
> are phrased as questions. This is a punctuation improvement, not an error —
> `char_diff: 1` in both cases.
>
> `google/gemma-3-27b-it`, KZ-07: the model correctly fixed the `kaz_to_rus`
> letters and separated the joined words, but left one letter slightly off
> from the published original (`char_diff: 1`) while still producing valid,
> readable Kazakh.

**Cheapest model that was good enough, and why:**

> `google/gemma-3-27b-it` is dramatically the cheapest option ($0.00020 for
> all eight sentences — roughly 29 times cheaper than deepseek and over 600
> times cheaper than qwen), and it got 6 out of 8 exactly right, including
> both homoglyph cases. However, its one clear failure — KZ-02, `drop_hyphen`
> — was not just a stylistic difference: the model returned the sentence
> completely unchanged, with an empty `changes` list, meaning it genuinely
> missed that error type rather than producing an equally valid alternative.
> That makes it a real correction failure, not just a scoring artifact.
>
> `deepseek/deepseek-v4-flash-0731` got a perfect 8 out of 8 with zero misses,
> at a cost of $0.00585 — still under a cent for the whole task, and about 21
> times cheaper than qwen while matching its accuracy exactly. Given that
> Gemma actually failed to detect one whole error category, I would call
> **deepseek the best "cheapest model that is good enough"**: its cost is
> still negligible in absolute terms, but unlike Gemma it did not miss any
> error type outright.

---

## Sublab Harder — open the tokenizer

### A. What a language costs

**`cl100k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|--------|-------|----------|-----------|-----------------------|
| kk | 200    | 263   | 0.760    | 3.75      | 0.166667              |
| ru | 129    | 277   | 0.466    | 2.30      | 0.107500              |
| en | 59     | 291   | 0.203    | 1.00      | 0.049167              |

**`o200k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|--------|-------|----------|-----------|-----------------------|
| kk | 84     | 263   | 0.319    | 1.58      | 0.070000              |
| ru | 74     | 277   | 0.267    | 1.32      | 0.061667              |
| en | 59     | 291   | 0.203    | 1.00      | 0.049167              |

### B. What a homoglyph does

One row per `latin_homoglyph` sentence in the dataset. Paste the actual decoded
token strings around the divergence point, not a description of them.

| Sentence id | Foreign char (index, name) | Tokens correct | Tokens corrupted | Δ  | Diverges at |
|---|---|----|-----|----|----|
|KZ-03 | (0, 'A', LATIN CAPITAL LETTER A); (2, 'a', LATIN SMALL LETTER A); (5, 't', LATIN SMALL LETTER T| 16 | 20  | +4 | 0  |
|KZ-08 |(1, 'o', LATIN SMALL LETTER O); (3, 'a', LATIN SMALL LETTER A); (9, 'T', LATIN CAPITAL LETTER T)  | 21 | 24  | +3 | 1  |

**Token pieces around the divergence:**

```
KZ-03
correct  : ['А', 'лая', 'қ', 'тарға', ' ақша']
corrupted: ['A', 'л', 'a', 'я', 'қ']
KZ-08
correct  : ['Д', 'он', 'аль', 'д', ' Т', 'рамп']
corrupted: ['Д', 'o', 'н', 'a', 'л', 'ль']
```

### C. Did it get better?

| Language | cl100k_base | o200k_base | Change |
|---|-------------|------------|-------|
| kk | 0.760       | 0.319      | -58%      |
| ru | 0.466       | 0.267      |   -43%    |
| en | 0.203       | 0.203      |    without changes   |

### Written answers

**1. What is the Kazakh tax?** The ratio against English in both encodings, the
dollar figure from A, and how much it changed between the two tokenizers.

>In `cl100k_base`, Kazakh text costs 3.75 times more tokens per character than English 
> ($0.166667 versus $0.049167 per 1000 sentences). In `o200k_base`, the gap has narrowed to 
> 1.58 times ($0.070000 versus $0.049167). The new tokenizer has nearly halved the “Kazakh tax” 
> (from 3.75x to 1.58x), but it hasn’t eliminated it entirely — Kazakh is still noticeably 
> more expensive than English, even in the newest encoding.

**2. Why did the models repair `kaz_to_rus` but struggle with
`latin_homoglyph`?** Both are single-letter substitutions and both look almost
identical on screen. Use your token streams from B as the evidence. Say what the
model actually received in each case.

>The difference is clearly visible in the tokens of measurement B. 
> The correct word “Алаяқтарға” is tokenized by the tokenizer into large, almost whole chunks: 
> `['А', 'лая', 'қ', 'тарға', ' ақша']` — the model sees the word almost as a whole. But in the 
> corrupted version with Latin `A` and `a`, the same word breaks down into individual letters: 
> `['A', 'л', 'a', 'я', 'қ']` — 4 tokens more for the same meaning. The same applies to "Дональд": 
> `['Д', 'он', 'аль', 'д']` turns into the fragmented `['Д', 'o', 'н', 'a', 'л', 'ль']`.
>The reason is that `kaz_to_rus` changes letters **within the same alphabet** (Cyrillic to Cyrillic), 
> and the tokenizer still reads the text as a coherent Cyrillic string. Whereas `latin_homoglyph` 
> substitutes letters with visually similar ones, but **from a different alphabet** (Latin) — the eye 
> doesn’t see a difference, but the tokenizer sees two different Unicode symbols and can’t combine 
> them into one familiar piece. As a result, the word breaks down into individual letters, and the 
> model receives not a word as input, but a jumble of symbols from two alphabets — this makes 
> it physically harder for the model to understand and correct it.

**3. Name one thing this measurement does not explain about your Sublab Medium
results.** You measured OpenAI's tokenizers; three of your six models were not
OpenAI's. What follows, and what would you have to do to close the gap?

>This measurement is based solely on OpenAI’s tokenizers (`cl100k_base`, `o200k_base`), 
> while three of the six tested models — Gemma, Qwen, and DeepSeek — use their own 
> tokenizers. This means that the conclusion about “the homoglyph breaks the word into letters” has 
> been proven only for the GPT family and does not necessarily transfer one‑to‑one to how these three 
> models perceive the same text. To bridge this gap, we need to find the official tokenizers for each
> of these models (for example, files `tokenizer.json` on HuggingFace for Qwen and DeepSeek, 
> SentencePiece tokenizer for Gemma) and repeat measurements A, B, and C using them.
