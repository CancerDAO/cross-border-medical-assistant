---
name: cancerdao-cross-border-medical-assistant
description: "当癌症患者描述在香港和内地都有就诊经历、需要整合病历或比较两地治疗时使用。整合散落的病历为完整时间线，提供香港vs内地药物可及性对比、费用估算、治疗方案比较。触发词：跨境医疗、香港内地看病、病历整合、时间线、赴港就医、香港买药、内地赴港、两地比较。"
license: MIT
metadata:
  author: CancerDAO
  version: "2.0.0"
  tags: cross-border medical oncology hong-kong china
---

# CancerDAO Cross-Border Medical Assistant

整合散落在香港和内地的病历为完整时间线，并提供治疗决策参考（费用、可及性）。

> ⚠️ **免责声明**：本工具提供信息综合，所有治疗决策须经临床医生确认。药物可及性和价格可能发生变化。

## When to use

- 在香港和内地都看过病，需要整合病历
- 比较香港和内地的治疗方案、费用
- 查询某药物在香港/内地的上市状态、价格
- 问"去香港治疗好还是留在内地"
- 提到香港医院（养和、港安等）+ 内地医院（省医、肿医等）

## Inputs

接受自由文本、文件夹路径、结构化 JSON。

## Outputs

### 1. 统一病历时间线（JSON）

每个条目：`date, institution, location, type, doctor, summary, diagnosis, treatment, medications, labs, imaging, notes`

### 2. 药物可及性对比表

| 药物 | 香港上市 | 内地上市 | 香港价格 | 内地价格 |
|---|---|---|---|---|
| 帕博利珠单抗 | ✅ | ✅ | HK$33,600/100mg | ¥17,918/100mg |
| 奥希替尼 | ✅ | ✅ | HK$18,000-25,000/月 | ¥5,000-6,000/月 |

### 3. 费用估算框架

| 类型 | 香港私立 | 内地三甲 |
|---|---|---|
| 专科门诊 | HKD 1,500–4,000/次 | CNY 100–300/次 |
| 住院（每日） | HKD 800–3,000/天 | CNY 100–500/天 |

### 4. 治疗方案对比 + 下一步建议

## 数据来源

| 来源 | 用途 |
|---|---|
| NMPA | 内地药物审批 |
| HK DOH | 香港药物注册 |
| ClinicalTrials.gov | 全球临床试验 |
| ChiCTR | 内地临床试验注册 |

## References

- `references/nmpa-drug-approval-guide.md`
- `references/hk-drug-registration-guide.md`
- `references/hk-cancer-hospitals.md`
- `references/drug-price-comparison.md`
- `references/cross-border-policy.md`

## File map

```
cancerdao-cross-border-medical-assistant/
├── SKILL.md
├── README.md
├── evals/
│   ├── evals.json
│   └── files/
└── references/
    ├── nmpa-drug-approval-guide.md
    ├── hk-drug-registration-guide.md
    ├── hk-cancer-hospitals.md
    ├── drug-price-comparison.md
    └── cross-border-policy.md
```
