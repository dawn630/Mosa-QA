# 情景问答口语评分数据集

本仓库发布一个面向中文口语情景问答自动评分任务的数据集。数据集用于研究学习者在给定生活化场景中的口语回答质量评估，支持总分预测与细粒度维度建模。

## 1. 数据集简介

每条样本包含以下核心信息：

- 题目背景
- 题目文本
- 学生回答的 ASR 文本
- 学生回答的人工转录文本
- 总分标签
- 各小题细粒度评分标签
- 样本唯一 `id`

该数据集适用于以下任务：

- 中文口语自动评分
- 多任务学习中的总分与细粒度联合预测
- ASR 文本与人工转录文本的对比研究
- 单问、双问、三问混合场景下的评分建模

## 2. 文件说明

当前目录包含以下主要文件：

- `最终数据_train.xlsx`：训练集 Excel 文件
- `最终数据_eval.xlsx`：测试集 Excel 文件
- `最终数据_train.json`：训练集 JSON 文件
- `最终数据_eval.json`：测试集 JSON 文件
- `最终数据.xlsx`：原始汇总工作簿

其中：

- `xlsx` 文件保留了原始工作簿中的主要文本与标签字段
- `json` 文件为公开发布时推荐直接使用的结构化版本
- `json` 文件是标准 JSON 数组格式，不是 JSONL

## 3. 数据规模

当前版本数据规模如下：

- 总样本数：`5663`
- 训练集：`3964`
- 测试集：`1699`

题目数量分布如下：

- 单问样本：`348`
- 双问样本：`4718`
- 三问样本：`597`

## 4. 划分方式

训练集与测试集采用固定划分，来源于项目中的最终实验切分版本。

- 随机种子：`42`
- 测试集比例：`0.3`

划分时综合考虑了以下因素：

- 总分分布
- 题目数量
- 题型信息

因此，该划分适合作为可复现的公开基准划分。

## 5. JSON 字段说明

`最终数据_train.json` 与 `最终数据_eval.json` 中的每条样本结构如下：

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

字段含义如下：

- `id`：样本唯一编号，对应原始工作簿中的 `序列`
- `background`：题目背景
- `question`：题目文本，可能包含 1 至 3 个子问题
- `student_answer_asr`：学生回答的 ASR 识别结果
- `student_answer_transcript`：学生回答的人工转录文本
- `score`：总分标签，范围 `0-8`
- `fine_grained_labels`：各小题细粒度标签

说明：

- 如果样本只有 1 个或 2 个小题，不存在的小题字段记为 `null`
- `student_answer_transcript` 由各小题人工转录文本按顺序拼接得到

## 6. 标签定义

数据集包含 1 个总分标签和 3 个细粒度评分维度。

### 6.1 总分

- `score`：范围 `0-8`

### 6.2 细粒度标签

每个小题包含以下三个维度：

- `completeness`：回答完整度，范围 `0-3`
- `relevance`：题目相关度，范围 `0-3`
- `pronunciation`：发音准确度，范围 `0-4`

补充说明：

- 原始 Excel 中的 `第一题题目流畅度 / 第二题题目流畅度 / 第三题题目流畅度` 在本数据集中统一映射为 `relevance`
- 这是因为该列在当前标注体系与实验处理中实际对应的是切题性 / 相关性维度

## 7. 使用建议

推荐优先使用 `json` 文件进行建模和评估，因为其结构更稳定，也更适合直接被 Python、PyTorch、Transformers 或其他训练框架读取。

一个简单的读取示例如下：

```python
import json

with open("最终数据_train.json", "r", encoding="utf-8") as f:
    train_data = json.load(f)

print(len(train_data))
print(train_data[0]["question"])
print(train_data[0]["fine_grained_labels"])
```

## 8. 公开说明

本次公开版本仅保留任务建模所需的核心文本与标签字段，不包含以下信息：

- 音频文件
- 音频本地路径
- 用户身份信息
- 性别、年龄、地区、就业情况等个人信息

如果后续需要发布音频版本，建议单独补充更严格的授权说明、脱敏策略与使用协议。

## 9. 使用限制与伦理说明

使用本数据集时请注意：

- 数据仅应用于学术研究与教育技术相关研究用途
- 请勿尝试还原、识别或关联任何个人身份信息
- 请勿将数据用于与评分任务无关的个体画像、身份推断或其他潜在敏感用途
- 使用者应遵守所在机构关于数据合规、隐私保护和研究伦理的相关要求

如果你计划在公开系统、商业系统或在线服务中使用本数据集，建议先补充必要的合规审核与授权确认。

## 10. 引用

如果你在论文、报告或项目中使用了本数据集，建议使用如下引用格式并根据你的论文信息补全：

```bibtex
@dataset{scenario_speaking_assessment_dataset,
  title     = {Scenario-based Chinese Spoken Response Assessment Dataset},
  author    = {Author Placeholder},
  year      = {2026},
  publisher = {GitHub},
  note      = {Train/eval split with overall and fine-grained labels}
}
```

## 11. 联系方式

如果你希望补充说明数据构建流程、标注规则、切分策略或实验设置，可以在仓库中进一步添加：

- `LICENSE`
- `CITATION.cff`
- `annotation_guidelines.pdf`
- `data_statement.md`

这会更适合正式公开与长期维护。
