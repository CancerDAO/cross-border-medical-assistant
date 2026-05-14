# cross-border-medical-assistant

CancerDAO skill for consolidating medical records and comparing cross-border (Hong Kong ↔ Mainland China) treatment options for cancer patients.

## What it does

- **Timeline consolidation**: Merge scattered records from HK hospitals, Mainland hospitals, labs, and imaging centers into a unified chronological timeline
- **Drug availability check**: Cross-reference NMPA (Mainland) and HK FDA drug approvals
- **Cost comparison**: Estimate consultation fees, drug prices, and admission costs in both regions
- **Treatment recommendation**: Provide structured comparison of HK vs Mainland treatment options, including clinical trial eligibility

## When to use

Trigger phrases: `跨境医疗`, `香港内地`, `两地看病`, `病历整合`, `时间线`, `赴港就医`, `香港买药`, `内地赴港`, `cross-border medical`

## Quick start

```bash
# Install via CancerDAO skills CLI
npx skills add CancerDAO/cross-border-medical-assistant

# Use in conversation
/cross-border-medical-assistant
```

## Project structure

```
cross-border-medical-assistant/
├── SKILL.md           # Skill definition and workflow
├── README.md          # This file
└── evals/
    ├── evals.json     # Evaluation test cases
    └── files/         # Mock patient data for testing
```

## Safety disclaimer

This tool provides informational synthesis only. All recommendations must be verified with licensed clinicians. Drug availability and pricing change frequently — always confirm with current sources before making treatment decisions.

## License

MIT