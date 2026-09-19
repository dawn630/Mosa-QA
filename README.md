# Audio Access and Privacy Notice

To protect learner privacy and comply with the relevant data-use restrictions, raw speech audio files are not included in this public repository. If you need access to the audio data for academic research or verification, please contact the dataset author by email:

**107552401324@stu.xju.edu.cn**

Please briefly describe your intended use and follow the applicable data-protection and research-ethics requirements.

## Sample Audio

For format verification and basic reproducibility checks, the repository may include a small set of representative audio samples under:

```text
sample_audio/
```

These files are provided only as example audio and do not represent the complete dataset. The sample files should be used together with their corresponding `id` values in `train.json` or `test.json`. Access to the complete audio collection remains restricted for privacy and data-use reasons. Please contact the dataset author by email if the full audio data are required for academic research or result verification.

# MOSA-QA: Chinese Spoken Scenario-Based Q&A Scoring Dataset

This repository releases a dataset for automatic assessment of Chinese spoken responses in scenario-based question-answering tasks. Each sample contains a scenario background, one to three questions, learner response transcriptions, an overall score, and fine-grained labels for each sub-question.

The dataset is intended for research on:

- Chinese spoken-language assessment
- Overall-score prediction
- Fine-grained multi-task assessment
- ASR-based spoken assessment
- Comparison between ASR text and manually corrected text
- Audio-language-model and text-language-model evaluation

## 1. Dataset Overview

The released dataset contains **5,663 valid samples**:

- Training set: `3,964` samples
- Evaluation set: `1,699` samples

The source workbook contained one trailing empty row. This row was excluded during preprocessing.

Each sample contains:

- `id`
- `background`
- `question`
- `student_answer_asr`
- `student_answer_transcript`
- `score`
- `fine_grained_labels`

The questions are scenario-based and may contain one, two, or three sub-questions.

## 2. Files

The current repository contains:

```text
train.json
test.json
README.md
```

The JSON files use standard JSON-array format rather than JSONL format.

In the released files:

- `train.json` contains 3,964 training samples.
- `test.json` contains 1,699 evaluation samples.

The repository release does not include raw audio files or personally identifying metadata.

## 3. Dataset Statistics

### 3.1 Overall-score distribution

| Score range | Samples | Ratio |
|---|---:|---:|
| 0–3 | 2,083 | 36.78% |
| 4–6 | 1,910 | 33.73% |
| 7–8 | 1,670 | 29.49% |
| **Total** | **5,663** | **100.00%** |

### 3.2 Number of sub-questions

According to the `fine_grained_labels` structure in the released JSON files:

| Number of questions | Samples | Ratio |
|---|---:|---:|
| 1 | 349 | 6.16% |
| 2 | 4,717 | 83.30% |
| 3 | 597 | 10.54% |
| **Total** | **5,663** | **100.00%** |

A sample is considered a one-question sample when only `q1` is present, a two-question sample when `q1` and `q2` are present, and a three-question sample when `q1`, `q2`, and `q3` are present.

## 4. Train/Evaluation Split

The released split follows the final experimental split:

- Random seed: `42`
- Evaluation-set ratio: `0.3`
- Training samples: `3,964`
- Evaluation samples: `1,699`

The split was constructed while monitoring:

- Overall-score distribution
- Number of sub-questions
- Question/item structure

This is a sample-level split. The same question template may occur in both sets, while learner responses are different. Therefore, the evaluation primarily measures scoring new learner responses for known platform questions, rather than generalization to completely unseen questions.

## 5. JSON Schema

Each entry has the following structure:

```json
{
  "id": "1",
  "background": "亮亮是个初中生，最近他喜欢玩网络游戏，只要有时间就去玩游戏。",
  "question": "亮亮遇到了什么问题？如果你是亮亮的朋友，你会怎么帮他？",
  "student_answer_asr": "亮亮遇到了他找我的朋友如果我亮亮如果交朋友较好的朋友",
  "student_answer_transcript": "亮亮遇到了大问题。\n如果我亮亮的话找朋友，找好的朋友，能的事情帮忙他",
  "score": 4,
  "fine_grained_labels": {
    "q1": {
      "completeness": 1,
      "relevance": 1,
      "pronunciation": 1
    },
    "q2": {
      "completeness": 2,
      "relevance": 1,
      "pronunciation": 1
    },
    "q3": null
  }
}
```

### Field descriptions

- `id`: Unique sample identifier corresponding to the original sample sequence.
- `background`: Scenario background.
- `question`: Question text containing one to three sub-questions.
- `student_answer_asr`: ASR output of the learner's spoken response.
- `student_answer_transcript`: Manually corrected transcription of the learner's response.
- `score`: Overall score in the range `0–8`.
- `fine_grained_labels`: Fine-grained labels for each sub-question.

For samples with fewer than three sub-questions, the corresponding question entry is `null`. The transcription fields contain the response text in the original sub-question order.

## 6. Label Definitions

### 6.1 Overall score

The overall score is an integer from `0` to `8`.

### 6.2 Fine-grained dimensions

Each sub-question has three labels:

- `completeness`: Response completeness, range `0–3`
- `relevance`: Relevance to the question, range `0–3`
- `pronunciation`: Pronunciation accuracy, range `0–4`

The original Excel fields named `题目流畅度` are represented as `relevance` in the released JSON because the annotation and experimental protocol use this dimension to measure how well the response addresses the question.

## 7. Prompt Construction Used in Experiments

The released JSON files contain structured data fields rather than a pre-rendered `prompt` field. During training and evaluation, prompts are deterministically constructed from `background`, `question`, `student_answer_asr`, and `student_answer_transcript`.

The following prompt text is the actual Chinese prompt used in the multi-task weighted-token-cross-entropy experiments.

### 7.1 Shared task instruction and scoring rubric

```text
你是一名专业的汉语语言评测专家，需要根据学生的口语回答进行细粒度评估。

评估任务包括：

1.给出学生回答的总分（0-8分之间的整数）。
2.对每个问题给出三个维度的评价标签：

  *回答完整度
  *题目相关度
  *发音准确度

评分规则如下：
【回答完整度】
3：句子结构完整；语法规范，仅有轻微错误，不影响理解。
2：句子基本完整，但存在部分语法成分缺失。
1：无法形成完整语法结构，仅为零散词语或短语。
0：几乎没有可理解的语法内容。

【题目相关度】
3：完整回答所有问题，内容紧密相关。
2：部分回答问题，但核心内容相关。
1：仅勉强涉及问题，相关度较低。
0：回答与问题无关。

【发音准确度】
4：发音规范，无理解障碍。
3：发音基本规范，偶有停顿。
2：存在一定偏差，但基本可理解。
1：偏差较多，但仍能捕捉主要信息。
0：发音偏差严重，难以理解。
```

### 7.2 Dual-transcription text-only prompt

In the dual-transcription text-only condition, the prompt contains the background, question, manual transcription, and ASR transcription. No audio input is used.

```text
你是一名专业的汉语语言评测专家，需要根据学生的口语回答进行细粒度评估。

评估任务包括：

1.给出学生回答的总分（0-8分之间的整数）。
2.对每个问题给出三个维度的评价标签：

  *回答完整度
  *题目相关度
  *发音准确度

你会获得以下信息：

*问题背景与问题
*学生回答的文本（人工转录）
*学生回答的文本（ASR识别结果）

请综合两种文本信息进行推理和评估。本次为 text-only 设定，不使用音频。人工转录文本通常更规范，ASR 识别结果更贴近自动识别场景；当两者不一致时，请结合两者进行稳健判断。

评分规则如下：
【回答完整度】
3：句子结构完整；语法规范，仅有轻微错误，不影响理解。
2：句子基本完整，但存在部分语法成分缺失。
1：无法形成完整语法结构，仅为零散词语或短语。
0：几乎没有可理解的语法内容。
【题目相关度】
3：完整回答所有问题，内容紧密相关。
2：部分回答问题，但核心内容相关。
1：仅勉强涉及问题，相关度较低。
0：回答与问题无关。
【发音准确度】
4：发音规范，无理解障碍。
3：发音基本规范，偶有停顿。
2：存在一定偏差，但基本可理解。
1：偏差较多，但仍能捕捉主要信息。
0：发音偏差严重，难以理解。

【问题背景】
{background}

【问题】
{question}

【学生回答文本（人工转录）】
{student_answer_transcript}

【学生回答文本（ASR识别结果）】
{student_answer_asr}

请根据评分规则进行评估。

请严格按照以下格式输出，不要输出多余解释：
分数：{0-8}
第一问：回答完整度：{0-3} 题目相关度：{0-3} 发音准确度：{0-4}
第二问：回答完整度：{0-3} 题目相关度：{0-3} 发音准确度：{0-4}
第三问：回答完整度：{0-3} 题目相关度：{0-3} 发音准确度：{0-4}
```

The output line for a non-existent sub-question is omitted. For example, a two-question sample contains only the `第一问` and `第二问` lines.

### 7.3 ASR-text condition

The ASR-text condition uses the same shared task instruction and scoring rubric. It provides:

```text
【问题背景】
{background}

【问题】
{question}

【学生回答文本（ASR识别结果）】
{student_answer_asr}
```

No manually corrected transcription is provided in this condition. This is the deployment-oriented text condition because ASR output is available at inference time while manual correction is not.

### 7.4 Audio-only prompt

The audio-only prompt uses the following actual input description:

```text
你是一名专业的汉语语言评测专家，需要根据学生的口语回答进行细粒度评估。

评估任务包括：

1.给出学生回答的总分（0-8分之间的整数）。
2.对每个问题给出三个维度的评价标签：

  *回答完整度
  *题目相关度
  *发音准确度

你会获得以下信息：

*问题背景与问题
*学生口语音频

请综合音频内容以及问题背景与问题进行推理和评估。本次不提供学生回答文本，不要假设存在额外的人工转写或 ASR 文本。

评分规则如下：
【回答完整度】
3：句子结构完整；语法规范，仅有轻微错误，不影响理解。
2：句子基本完整，但存在部分语法成分缺失。
1：无法形成完整语法结构，仅为零散词语或短语。
0：几乎没有可理解的语法内容。
【题目相关度】
3：完整回答所有问题，内容紧密相关。
2：部分回答问题，但核心内容相关。
1：仅勉强涉及问题，相关度较低。
0：回答与问题无关。
【发音准确度】
4：发音规范，无理解障碍。
3：发音基本规范，偶有停顿。
2：存在一定偏差，但基本可理解。
1：偏差较多，但仍能捕捉主要信息。
0：发音偏差严重，难以理解。

【问题背景】
{background}

【问题】
{question}

请根据评分规则进行评估。

请严格按照以下格式输出，不要输出多余解释：
分数：{0-8}
第一问：回答完整度：{0-3} 题目相关度：{0-3} 发音准确度：{0-4}
第二问：回答完整度：{0-3} 题目相关度：{0-3} 发音准确度：{0-4}
第三问：回答完整度：{0-3} 题目相关度：{0-3} 发音准确度：{0-4}
```

The speech waveform is supplied as a separate model input. The audio-only prompt does not insert either `student_answer_asr` or `student_answer_transcript`.

## 8. Supervised Fine-Tuning Target Format

The target sequence used for structured generation follows the same format as the output constraint:

```text
分数：4
第一问：回答完整度：1 题目相关度：1 发音准确度：1
第二问：回答完整度：2 题目相关度：1 发音准确度：1
```

For a three-question sample:

```text
分数：7
第一问：回答完整度：3 题目相关度：3 发音准确度：3
第二问：回答完整度：3 题目相关度：3 发音准确度：3
第三问：回答完整度：2 题目相关度：2 发音准确度：2
```

The target contains one overall score and the available fine-grained labels. Non-existent questions are not generated.

## 9. Recommended Experimental Conditions

For a controlled comparison, use the same background, question, scoring rubric, output format, training split, evaluation split, and random seed. Change only the response information:

| Condition | Background | Question | ASR text | Manual text | Audio |
|---|---:|---:|---:|---:|---:|
| ASR text-only | Yes | Yes | Yes | No | No |
| Manual text-only | Yes | Yes | No | Yes | No |
| Dual text-only | Yes | Yes | Yes | Yes | No |
| Audio-only | Yes | Yes | No | No | Yes |

The ASR-only condition is the most deployment-relevant condition. The manual-only and dual-text conditions should be interpreted as controlled text-input or information-enriched conditions.

## 10. Loading the Dataset

```python
import json

with open("train.json", "r", encoding="utf-8") as f:
    train_data = json.load(f)

with open("test.json", "r", encoding="utf-8") as f:
    test_data = json.load(f)

print(len(train_data))
print(train_data[0]["background"])
print(train_data[0]["question"])
print(train_data[0]["student_answer_asr"])
print(train_data[0]["student_answer_transcript"])
print(train_data[0]["fine_grained_labels"])
```

## 11. Evaluation Metrics

For the overall score, we recommend reporting:

- Pearson correlation coefficient (PCC)
- Quadratic weighted kappa (QWK)
- Cohen's kappa
- Mean absolute error (MAE)
- Root mean squared error (RMSE)
- Exact-match rate
- Within-1 accuracy
- Within-2 accuracy

For each fine-grained label and each merged dimension, we recommend reporting:

- Accuracy
- Cohen's kappa
- QWK
- PCC
- Macro-F1
- Weighted-F1

## 12. Data Limitations and Release Notes

The current split is sample-level and contains repeated question templates across training and evaluation sets. It should therefore be interpreted as a benchmark for scoring new learner responses to known questions.

The released JSON files do not contain raw audio, user identifiers, gender, age, residence, or employment information. Users should not attempt to infer personal identity or language background from the released fields.

The transcription fields represent two different information sources:

- `student_answer_asr` reflects automatic recognition output.
- `student_answer_transcript` reflects manually corrected text when available.

For a small number of records where one transcription was unavailable in the source material, the released preprocessing version may use the available transcription as a fallback. Such fallback records should not be interpreted as independently annotated manual transcriptions.

## 13. Ethical Use

This dataset is released for academic research and educational technology research.

Users should:

- Follow applicable data-protection and research-ethics requirements.
- Avoid identity reconstruction or re-identification.
- Avoid using the data for individual profiling unrelated to spoken assessment.
- Report the exact transcription condition used in experiments.
- Clearly distinguish ASR-only, manual-transcription, dual-text, and audio-based settings.

## 14. Citation

If you use this dataset, please cite the corresponding MOSA-QA paper. A BibTeX entry can be added after the final publication metadata becomes available:

```bibtex
@dataset{mosa_qa,
  title     = {MOSA-QA: Chinese Spoken Scenario-Based Q&A Scoring Dataset},
  author    = {Anonymous Authors},
  year      = {2026},
  publisher = {GitHub},
  note      = {Dataset release with overall and fine-grained spoken-response labels}
}
```
