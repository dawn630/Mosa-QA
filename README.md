# Spoken Q&A Scoring Dataset
This repository releases a dataset for the automatic scoring of **Chinese spoken scenario-based question answering**. It is designed to evaluate the quality of learners' oral responses in real-life scenarios, and supports overall score prediction as well as fine-grained dimension modeling.

## 1. Dataset Overview
Each data sample consists of the core information below:
- Context background
- Question content
- ASR transcription of the learner's response
- Manually corrected transcription of the learner's response
- Overall score label
- Fine-grained score labels for each sub-question
- Unique sample ID

This dataset is applicable to the following research tasks:
- Automatic scoring for Chinese spoken language
- Joint prediction of overall scores and fine-grained metrics in multi-task learning
- Comparative analysis between ASR transcripts and human-corrected transcripts
- Scoring modeling for mixed scenarios with 1, 2 or 3 questions

## 2. File Description
The main files in the current directory are listed as follows:
- `train.xlsx`: Training set (Excel format)
- `test.xlsx`: Evaluation set (Excel format)
- `train.json`: Training set (JSON format)
- `test.json`: Evaluation set (JSON format)
Notes:
- Excel files retain major text content and label fields from the original workbook.
- JSON files are the recommended structured versions for public release.
- The JSON files adopt standard JSON array format, not JSONL.

## 3. Dataset Statistics
Statistics of the current version:
- Total samples: 5663
- Training set: 3964
- Evaluation set: 1699

Distribution by number of questions per sample:
- Samples with 1 question: 348
- Samples with 2 questions: 4718
- Samples with 3 questions: 597

## 4. Data Splitting
The training and evaluation sets use a fixed split adopted in the final experimental version of the project.
- Random seed: 42
- Test set ratio: 0.3

The splitting process takes the following factors into account:
- Distribution of overall scores
- Number of questions per sample
- Question type information

This split serves as a reproducible standard benchmark for public use.

## 5. JSON Field Specification
The structure of each entry in `最终数据_train.json` and `最终数据_eval.json` is shown below:
```json
{
  "id": "1",
  "background": "......",
  "question": "......",
  "student_answer_asr": "......",
  "student_answer_transcript": "......",
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

Field explanations:
- `id`: Unique sample ID, corresponding to the serial number in the original workbook
- `background`: Scenario background
- `question`: Question text, containing 1 to 3 sub-questions
- `student_answer_asr`: Raw ASR output of the learner's response
- `student_answer_transcript`: Manually revised transcription of the learner's response
- `score`: Overall score label (range: 0-8)
- `fine_grained_labels`: Fine-grained scores for each sub-question

Additional notes:
- For samples with only 1 or 2 sub-questions, the fields for non-existent questions are marked as `null`.
- `student_answer_transcript` is concatenated from manual transcripts of all sub-questions in order.

## 6. Label Definition
The dataset includes **one overall score** and **three fine-grained scoring dimensions**.

### 6.1 Overall Score
- `score`: Value range 0-8

### 6.2 Fine-Grained Labels
Each sub-question is evaluated across three dimensions:
- `completeness`: Response completeness (range: 0-3)
- `relevance`: Task relevance (range: 0-3)
- `pronunciation`: Pronunciation accuracy (range: 0-4)

Supplementary explanation:
The fields named *Fluency of Question 1 / Question 2 / Question 3* in the original Excel files are uniformly mapped to `relevance` in this dataset. In accordance with the annotation rules and experimental settings, these fields actually measure how well responses align with the given questions.

## 7. Usage Recommendations
It is recommended to use JSON files for model training and evaluation. They feature more stable structure and can be directly parsed by
