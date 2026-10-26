# EHR Data Specification: Guideline-Concordance NLP Annotation Schema
**Author:** Nadim Aoun, MD  
**Core Domain:** Clinical NLP Taxonomy Development, Automated Chart Abstraction, and Model Benchmarking  

---

## 📋 Project Context & Scope
This framework establishes a standardized annotation schema and clinical taxonomy designed to train, evaluate, and benchmark Natural Language Processing (NLP) models. The technical objective is to enable automated systems to critically parse unstructured Electronic Health Record (EHR) progress notes, identify eligible patients missing guideline-directed chronic care therapies, and accurately classify the underlying rationale for the care gap.

---

## 🔍 The Labeled Data Schema: Care Gap Taxonomy Mapping

To build an algorithmic benchmark for automated medical quality auditing, unstructured clinical text must be mapped to distinct, mutually exclusive categorical classifications:

| Taxonomy Code | Classification | Labeled Ground Truth Definition (Model Pass Criteria) | Automated System Target Vector |
| :--- | :--- | :--- | :--- |
| **TAX-01** | **Undocumented Gap** | Longitudinal chart data confirms guideline eligibility via laboratory/comorbidity inputs, but features zero documented provider discussion or rationale. | Flags systemic operational screening failures and logic omissions. |
| **TAX-02** | **Patient Refusal** | Unstructured clinical notes explicitly state the patient declined initiation due to personal preference, lifestyle goals, or holistic autonomy. | Identifies cohorts for targeted behavioral outreach and counseling workflows. |
| **TAX-03** | **True Intolerance** | Documented clinical history of severe adverse physiological responses or objective physiological contraindications to the medication class. | Safely segregates true medical contraindications from systemic process failures. |
| **TAX-04** | **Risk Misconception** | Note reflects patient-reported fear or hesitation regarding unverified side-effect profiles or dynamic misunderstandings of long-term risk. | Triggers automated clinical educational content deployment loops. |
| **TAX-05** | **Clinical Non-Adherence** | The target therapeutic class is actively prescribed, but longitudinal chart notes confirm a pattern of failure to fill or take regularly. | Triggers digital health tracking and pharmacy coordination workflows. |

---

## ⚙️ Automated Benchmarking & Evaluation Architecture
```text
        [ Synthetic Unstructured EMR Records ]
          (Progress Notes, Med Lists, Labs)
                         │
                         ▼
        ┌────────────────────────────────┐
        │  AI Model Automated Extraction │
        └────────────────┬───────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │  Physician Reference Standard  │
        │  (Adjudicated Classification)  │
        └────────────────┬───────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │  Comparison & Error Analysis   │
        │ - Score Annotation Agreement   │
        │ - Audit Safety-Critical Errors │
        └────────────────────────────────┘
```


## 🔄 Reusable Regression Checks for Engineering Teams

When training an automated LLM judge against this data taxonomy, engineering teams must execute these validation tests to evaluate model alignment:

1. **The Implicit Ambiguity Test:** Feed the model a note where a patient briefly mentions *"worrying about transient muscle pain"* but the provider doesn't document an explicit diagnosis of clinical myopathy. *The model must not misclassify this as true Intolerance (TAX-03); it must map it to a Misconception or Lack of Explicit Documentation (TAX-04/TAX-01).*
2. **The Temporal Discrepancy Test:** Provide a historical note showing a patient declined a therapy in 2024, but a 2026 laboratory profile shows a new, severe disease marker that went unaddressed. *The model must recognize the temporal shift and flag a fresh screening gap rather than anchoring on the outdated refusal.*
3. **The Data Contradiction Test:** Present a conflicting chart where a structured medication list shows an active prescription but the unstructured copy-forward text states "patient refuses all medications." *The model must flag the documentation logic conflict for human-in-the-loop adjudication.*
