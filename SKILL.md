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

触发以下场景时激活本技能：

- 在香港和内地都看过病，需要整合病历
- 比较香港和内地的治疗方案、费用
- 查询某药物在香港/内地的上市状态、价格
- 问"去香港治疗好还是留在内地"
- 提到"养和医院"、"港安医院"等香港医院 + "广东省人民医院"等内地医院

## Inputs

接受以下格式：
- **自由文本**：描述就诊经历（"我在香港养和看过专科，用过帕博利珠单抗，后来在广东省人民医院住过院"）
- **文件夹路径**：包含病历文件的目录
- **结构化 JSON**：已知 schema 的患者档案

## Outputs

### 1. 统一病历时间线（JSON）

```json
{
  "patient_id": "anonymous",
  "generated_at": "ISO 8601",
  "timeline": [
    {
      "date": "2024-08-15",
      "institution": "香港养和医院",
      "location": "HK",
      "type": "consultation | admission | lab | imaging | pharmacy",
      "doctor": "Dr. XXX",
      "summary": "...",
      "diagnosis": null,
      "treatment": null,
      "medications": [],
      "labs": null,
      "imaging": null,
      "notes": null
    }
  ],
  "drug_availability": [...],
  "cost_comparison": {...}
}
```

### 2. 药物可及性对比表

| 药物 | 香港上市 | 内地上市 | 香港价格 | 内地价格 | 备注 |
|---|---|---|---|---|---|
| 帕博利珠单抗 | ✅ 已注册 | ✅ 已获批 | HK$33,600/100mg | ¥17,918/100mg | — |

详见：`references/drug-price-comparison.md`、`references/nmpa-drug-approval-guide.md`、`references/hk-drug-registration-guide.md`

### 3. 费用估算框架

| 类型 | 香港私立 | 内地三甲 |
|---|---|---|
| 专科门诊 | HKD 1,500–4,000/次 | CNY 100–300/次 |
| 住院（每日） | HKD 800–3,000/天 | CNY 100–500/天 |
| 帕博利珠单抗 | ~HK$33,600/100mg | ~¥17,918/100mg |

详见：`references/hk-cancer-hospitals.md`、`references/cross-border-policy.md`


### 4. 治疗方案对比

- 两地各自的可用药物/试验
- 主诊医师信息（姓名、专科）
- 下一步建议（含临床确认 disclaimer）


## Workflow


### Step 1 — 收集病历

从用户输入中提取所有可用信息：

| 来源 | 记录类型 |
|---|---|
| 香港医院（养和、港安、玛丽等）| 专科门诊、住院小结、用药记录、影像报告 |
| 内地医院 | 入院记录、出院小结、检验报告、病理报告 |
| 检验/影像机构 | 血液检查、基因检测、CT/MRI/PET-CT |
| 药房 | 购药记录、用药清单 |

### Step 2 — 标准化为时间线

每个条目标准化为：
```
(date, institution, location, type, doctor, summary,
 diagnosis, treatment, medications, labs, imaging, notes)
```

- 日期转化为 ISO 格式
- 去重重叠条目
- 不确定日期标注 `date_confidence: "low"`


### Step 3 — 药物可及性查询


对时间线中出现的每种药物，查询：

| 数据库 | 用途 |
|---|---|
| NMPA（国家药品监督管理局） | 确认内地上市状态（参考：`references/nmpa-drug-approval-guide.md`） |
| HK DOH（香港卫生署） | 确认香港注册状态（参考：`references/hk-drug-registration-guide.md`） |
| FDA（美国） | 全球参考 |

详见：`references/drug-price-comparison.md`

### Step 4 — 费用估算

基于收集的信息估算：
- 咨询费（两地为参考范围，非精确数字）
- 药费（含两地差价）
- 住院费（如适用）
- 医保/保险覆盖情况

### Step 5 — 输出综合报告


1. 统一时间线 JSON
2. 药物对比表（含价格、上市状态）
3. 费用场景对比
4. 下一步建议（含免责声明）
5. **未验证声明处理**：若患者提及的药物或试验无法通过参考数据库验证，须明确说明"无法验证该药物/试验的存在"，并提供已知同类候选药物/试验。禁止在无法验证时声称该药物/试验存在。

## 数据来源

| 来源 | URL | 用途 |
|---|---|---|
| NMPA | https://www.nmpa.gov.cn | 内地药物审批 |
| HK DOH | https://www.dh.gov.hk | 香港药物注册 |
| ClinicalTrials.gov | https://clinicaltrials.gov | 全球临床试验 |
| ChiCTR | https://www.chictr.org.cn | 内地临床试验注册 |

## 常见错误

| 错误 | 正确做法 |
|---|---|
| 假设患者需要治疗决策 | 先完成病历整合，确认患者核心需求 |
| 给出精确价格 | 说明价格是参考范围，非精确数字 |
| 不注明信息来源 | 每条信息附来源（机构+访问日期） |

## References

- `references/nmpa-drug-approval-guide.md` — NMPA 数据库使用、药物审批路径
- `references/hk-drug-registration-guide.md` — 香港卫生署药物注册查询
- `references/hk-cancer-hospitals.md` — 香港主要癌症中心、病房费、诊费
- `references/drug-price-comparison.md` — Keytruda、EGFR TKI 等价格对比
- `references/cross-border-policy.md` — 内地患者赴港就医政策、通关、支付

## File map

```
cancerdao-cross-border-medical-assistant/
├── SKILL.md
├── README.md
├── evals/
│   ├── evals.json
│   └── files/
│       └── mock_patient_records/
└── references/
    ├── nmpa-drug-approval-guide.md
    ├── hk-drug-registration-guide.md
    ├── hk-cancer-hospitals.md
    ├── drug-price-comparison.md
    └── cross-border-policy.md
```
