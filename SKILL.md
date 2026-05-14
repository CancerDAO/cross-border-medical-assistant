---
name: cross-border-medical-assistant
description: "整合散落在不同城市、不同医院的病历成一条完整时间线，同时判断某个病该去香港还是内地、两边费用差多少、哪些药物可及性不同。Triggers on: 跨境医疗, 香港内地, 两地看病, 病历整合, 时间线, 赴港就医, 香港买药, 内地赴港"
license: MIT
metadata:
  author: CancerDAO
  version: "1.0.0"
---

# Cross-Border Medical Assistant

## When to Use

**Trigger phrases:**

| Chinese | English |
|---|---|
| 跨境医疗 | cross-border medical |
| 香港内地看病 | Hong Kong vs Mainland treatment |
| 两地看病 | see doctor in both HK and Mainland |
| 病历整合 | consolidate medical records |
| 时间线 | timeline |
| 赴港就医 | seek treatment in Hong Kong |
| 香港买药 | buy drugs in Hong Kong |
| 内地赴港 | travel from Mainland to HK for treatment |

**Use when:**
- Patient has received treatment across Hong Kong and Mainland China
- Patient wants to compare treatment options between HK and Mainland
- Patient needs drug availability comparison (HK FDA vs NMPA)
- Patient requests cost comparison for cross-border treatment

## Inputs

**Accepted formats:**
- Free-text description of medical history (natural language)
- Folder path containing medical records (PDF, images, lab reports)
- Structured JSON with known schema

**Example free-text input:**
> "我在香港养和医院看过专科，用过帕博利珠单抗，后来在广东省人民医院住过院做过化疗。现在想去香港继续治疗能把病历整合给我吗？"

**Example structured input:**
```json
{
  "hk_records": [...],
  "mainland_records": [...],
  "current_medications": [...],
  "gene_mutations": [...]
}
```

## Outputs

### 1. Unified Medical Timeline (JSON)

```json
{
  "patient_id": "anonymous",
  "generated_at": "2026-05-14T00:00:00Z",
  "timeline": [
    {
      "date": "2024-01-15",
      "institution": "香港养和医院",
      "location": "HK",
      "type": "consultation",
      "doctor": "Dr. XXX",
      "summary": "专科门诊",
      "diagnosis": null,
      "treatment": null,
      "medications": [],
      "labs": null,
      "imaging": null,
      "notes": null
    },
    {
      "date": "2024-03-01",
      "institution": "广东省人民医院",
      "location": "Mainland",
      "type": "admission",
      "doctor": null,
      "summary": "住院化疗",
      "diagnosis": "肺腺癌IIIB",
      "treatment": "PC方案化疗",
      "medications": ["培美曲塞+卡铂"],
      "labs": {...},
      "imaging": {...},
      "notes": null
    }
  ],
  "drug_availability": [...],
  "cost_comparison": {...}
}
```

### 2. Cross-Border Comparison Report

- Drug availability comparison (HK FDA vs NMPA)
- Cost estimate ranges (consultation, drugs, admission)
- Key decision factors for treatment location choice
- Clinical trial availability in both regions

### 3. Recommendation Summary

- Synthesis of treatment history
- Identified gaps (missing records, pending results)
- Recommended next steps (with clinician consultation disclaimer)

## Workflow

### Step 1 — Record Collection

Collect medical records from all available sources:

| Source | Location | Record Types |
|---|---|---|
| HK hospitals (e.g., 养和, 港安, 玛丽) | Hong Kong | 专科门诊、住院小结、用药记录、影像报告 |
| Mainland hospitals | Mainland | 入院记录、出院小结、检验报告、病理报告 |
| Labs | Both | 血液检查、基因检测报告 |
| Imaging centers | Both | CT/MRI/PET-CT reports |
| Pharmacy records | Both | 购药记录、用药清单 |

### Step 2 — Normalization into Chronological Timeline

Normalize each record into a standard entry:

```
(date, institution, location, type, doctor, summary,
 diagnosis, treatment, medications, labs, imaging, notes)
```

- Deduplicate entries with overlapping dates
- Convert all dates to ISO format (YYYY-MM-DD)
- Flag uncertain dates with `date_confidence: "low"`

### Step 3 — Drug Availability Cross-Reference

Cross-reference all medications against:

| Database | Scope | Use |
|---|---|---|
| NMPA (国家药品监督管理局) | Mainland approvals | Confirm Mainland drug status |
| HK DOH (香港卫生署) | HK drug registrations | Confirm HK drug status |
| FDA (US) | Reference | Global reference |

For each medication, note:
- HK FDA approval status and date
- NMPA approval status and date
- Price in HK (HKD) and Mainland (CNY)
- Pending trials or awaiting approvals

### Step 4 — Cost Comparison Framework

Estimate total cost for treatment scenarios:

**Consultation costs:**
- HK private specialist: HKD 1,500–4,000 / visit
- Mainland tertiary hospital specialist: CNY 100–300 / visit

**Drug costs (examples):**
- 帕博利珠单抗 (Pembrolizumab): HK ~HKD 45,000/cycle, Mainland ~CNY 18,000/cycle (varies by indication)
- 伏美替尼 (Furmonertinib): Mainland ~CNY 8,000/month; HK availability varies

**Admission costs:**
- HK private hospital: HKD 800–3,000/day (room and board)
- Mainland public hospital: CNY 100–500/day

**Overall cost factors:**
- Insurance coverage (Mainland医保 / HK医疗保险)
- Drug procurement channels (公立医院 vs 私家药房)
- Clinical trial eligibility (often free drug + cover costs)

### Step 5 — Generate Outputs

1. **Unified timeline JSON** — all records in chronological order
2. **Drug comparison table** — availability and price in both regions
3. **Cost scenario comparison** — HK vs Mainland for each treatment option
4. **Recommendation report** — structured advice with clear disclaimers

## Data Sources

| Source | URL | Data |
|---|---|---|
| NMPA (国家药品监督管理局) | https://www.nmpa.gov.cn | Mainland drug approvals |
| HK DOH (香港卫生署) | https://www.dh.gov.hk | HK drug registrations |
| ClinicalTrials.gov | https://clinicaltrials.gov | Global trial listings |
| CIViC (via oncology-db MCP) | MCP tool | Gene-variant treatment evidence |
| 中国临床试验注册中心 | https://www.chictr.org.cn | Mainland trial registrations |

## Safety & Disclaimers

**IMPORTANT:**
- This tool provides informational synthesis only
- All recommendations must be verified with licensed clinicians
- Drug availability and pricing change frequently — always confirm with current sources
- Treatment decisions should be made with oncologist guidance
- This tool does not replace professional medical advice

**Required disclaimer in all outputs:**
> "以上信息仅供参考，请与您的临床医生确认后再做治疗决定。药物可及性和价格可能发生变化，请以当地最新公布信息为准。"

## Edge Cases

| Situation | Handling |
|---|---|
| Missing dates | Use context clues; flag as approximate with confidence level |
| Conflicting diagnoses | List both with source; note discrepancy |
| Drug only in one region | Highlight availability gap; note implications |
| Non-standard medical terms | Attempt normalization; flag unrecognized terms |
| Large volume of records | Summarize key events; note full timeline available on request |