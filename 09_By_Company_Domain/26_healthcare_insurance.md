# Top Python Questions — Healthcare / Insurance Companies
> Target: 5–15 Years | Optum, Cigna, Aetna, Humana, Carelon, CVS Health, UHG, Elevance, Molina, BCBS
> Focus: HIPAA compliance, claims processing, eligibility, prior auth, clinical data, GenAI for healthcare
> Sources: Glassdoor, LinkedIn, Reddit, Healthcare Engineering Blogs

---

## What Healthcare/Insurance Interviews Look Like

Healthcare companies test for:
- Strong Python for data-intensive workloads (Pandas, SQL, ETL)
- REST API design for healthcare systems (FHIR, HL7, claims APIs)
- HIPAA compliance understanding (PHI, audit logs, data masking)
- Domain knowledge: claims lifecycle, eligibility, prior auth, adjudication
- Database proficiency: PostgreSQL for transactional, MongoDB for documents
- Cloud (AWS/Azure) with healthcare-specific services
- Increasingly: GenAI for claims automation, prior auth, clinical decision support

**Rounds:** Technical screen → Coding challenge → System design → Domain knowledge → Leadership

---

## SECTION 1: Healthcare Domain Python (Q1–Q25)

### Q1
- **Question:** Explain the healthcare claims lifecycle. How would you model it in Python?
- **Level:** 5–8 Yrs | **Tier:** T1
- **Answer:**
  ```python
  from enum import Enum
  from pydantic import BaseModel
  from datetime import date

  class ClaimStatus(str, Enum):
      SUBMITTED = "submitted"
      VALIDATED = "validated"
      ELIGIBILITY_VERIFIED = "eligibility_verified"
      PRIOR_AUTH_CHECKED = "prior_auth_checked"
      ADJUDICATED = "adjudicated"
      PAID = "paid"
      DENIED = "denied"
      APPEALED = "appealed"

  class Claim(BaseModel):
      claim_id: str
      member_id: str
      provider_npi: str
      service_date: date
      diagnosis_codes: list[str]   # ICD-10
      procedure_codes: list[str]   # CPT/HCPCS
      billed_amount: float
      status: ClaimStatus = ClaimStatus.SUBMITTED
      plan_code: str
      line_of_business: str       # Medicare, Medicaid, Commercial
  ```
- **Follow-Ups:** How do you handle HIPAA PHI in this model? What fields are PHI?

---

### Q2
- **Question:** What is HIPAA? What is PHI? How do you handle PHI in a Python service?
- **Level:** 5–12 Yrs | **Tier:** T1
- **Answer Points:**
  - PHI (Protected Health Information): any data that identifies a patient + health info
  - PHI identifiers: name, DOB, SSN, MRN, address, phone, email, dates (except year), IP address
  - **Handling PHI in Python:**
    - Encrypt at rest: database-level encryption + field-level for sensitive columns
    - Encrypt in transit: TLS 1.2+ everywhere; no HTTP
    - Mask in logs: never log PHI; use patient_id (not name/SSN) in logs
    - Access control: RBAC; minimum necessary access; audit every access
    - Before sending to LLM: scrub PHI using NLP/regex/presidio; use de-identified data
  ```python
  import re
  from presidio_analyzer import AnalyzerEngine
  from presidio_anonymizer import AnonymizerEngine

  analyzer = AnalyzerEngine()
  anonymizer = AnonymizerEngine()

  def mask_phi(text: str) -> str:
      results = analyzer.analyze(text=text, language='en',
                                  entities=["PERSON", "DATE_TIME", "EMAIL_ADDRESS", "PHONE_NUMBER"])
      return anonymizer.anonymize(text=text, analyzer_results=results).text
  ```
- **Difficulty:** Medium

---

### Q3
- **Question:** How do you model HL7 FHIR data in Python? Parse a FHIR Patient resource.
- **Level:** 8–12 Yrs | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel
  from typing import Optional
  from datetime import date

  class FHIRHumanName(BaseModel):
      use: str
      family: str
      given: list[str]

  class FHIRIdentifier(BaseModel):
      system: str
      value: str

  class FHIRPatient(BaseModel):
      resourceType: str = "Patient"
      id: str
      identifier: list[FHIRIdentifier]
      name: list[FHIRHumanName]
      gender: str
      birthDate: date
      active: bool = True

  # Parse from JSON
  fhir_json = {"resourceType": "Patient", "id": "P001", ...}
  patient = FHIRPatient(**fhir_json)
  ```
- **Tools:** `fhir.resources` Python library for full FHIR R4 model validation

---

### Q4
- **Question:** How do you implement an eligibility verification service in Python?
- **Level:** 8–12 Yrs | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel
  from datetime import date

  class EligibilityRequest(BaseModel):
      member_id: str
      service_date: date
      service_type_code: str  # e.g., "30" = Health Benefit Plan Coverage

  class EligibilityResponse(BaseModel):
      eligible: bool
      plan_name: str
      plan_code: str
      coverage_start: date
      coverage_end: date | None
      copay: float
      deductible: float
      deductible_met: float
      out_of_pocket_max: float
      out_of_pocket_met: float
      prior_auth_required: bool
      network_status: str  # "in_network" | "out_of_network"

  @app.post("/eligibility/verify", response_model=EligibilityResponse)
  async def verify_eligibility(
      request: EligibilityRequest,
      db: AsyncSession = Depends(get_db)
  ):
      member = await db.get(Member, request.member_id)
      if not member or not member.is_active_on(request.service_date):
          raise HTTPException(404, "Member not found or not eligible")
      coverage = await get_coverage(member, request.service_date)
      return EligibilityResponse(**coverage.to_response())
  ```
- **Difficulty:** Medium

---

### Q5
- **Question:** How do you implement a prior authorization service in Python?
- **Level:** 8–12 Yrs | **Tier:** T1
- **Answer Points:**
  - PA request: member_id, provider, procedure_code, diagnosis_codes, supporting_docs
  - Validate PA is required for the procedure/plan combination
  - Check clinical criteria against approved guidelines
  - Auto-approve if criteria met + low value; route to clinical review if uncertain
  - Track: submission date, due date (72hr/urgency), decision, reason, audit log
  - Integrate with GenAI agent for automated clinical criteria matching
- **Difficulty:** Medium

---

### Q6–Q25 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 6 | How do you compute a member's out-of-pocket maximum in Python? | Claims Logic | T1 |
| 7 | How do you implement ICD-10 code validation in a Python claims API? | Healthcare Data | T1 |
| 8 | How do you implement CPT code lookup and fee schedule in Python? | Claims Logic | T1 |
| 9 | How do you process an 835 EDI remittance file in Python? | EDI | T2 |
| 10 | How do you process an 837 EDI claim file in Python? | EDI | T2 |
| 11 | How do you implement a COB (Coordination of Benefits) calculation? | Claims Logic | T2 |
| 12 | How do you implement network status checking (in/out of network) for a provider? | Claims Logic | T1 |
| 13 | How do you implement a drug formulary lookup API in Python? | Pharmacy | T1 |
| 14 | How do you detect duplicate claims in Python? What rules do you apply? | Claims Logic | T1 |
| 15 | How do you implement audit logging for PHI access in Python? | HIPAA | T1 |
| 16 | How do you implement data encryption for PHI fields in PostgreSQL from Python? | HIPAA | T1 |
| 17 | How do you implement a member search that masks PHI in logs? | HIPAA | T1 |
| 18 | How do you implement a FHIR R4 REST API in Python? | FHIR | T1 |
| 19 | How do you handle HL7 v2.x message parsing in Python? | HL7 | T2 |
| 20 | How do you implement clinical coding (ICD/CPT) with an LLM + RAG pipeline? | GenAI | T1 |
| 21 | How do you implement a claims fraud detection rule engine in Python? | Claims Logic | T1 |
| 22 | How do you implement SLA tracking for prior authorization turnaround times? | Operations | T1 |
| 23 | How do you implement member portal API authentication (SSO + MFA)? | Auth | T1 |
| 24 | How do you handle CMS STAR ratings data in a Python analytics pipeline? | Analytics | T2 |
| 25 | How do you implement a risk adjustment calculation for Medicare Advantage? | Analytics | T2 |

---

## SECTION 2: Data Engineering for Healthcare (Q26–Q50)

### Q26
- **Question:** You receive a 10M-row claims CSV daily from a clearinghouse. Design the ingestion pipeline.
- **Tier:** T1
- **Answer Points:**
  - SFTP pickup → validate file (row count, checksum) → decompress
  - Pydantic schema validation on sample rows; schema drift detection
  - Chunked reading (Pandas 50K rows/chunk or Polars full file)
  - PHI handling: mask before loading to non-prod environments
  - Upsert to PostgreSQL with `ON CONFLICT DO UPDATE` by claim_id
  - Dead letter: invalid rows → error table with reason; alert if > 1% rejection
  - Audit log: file received time, row count, processed count, errors
  - Airflow DAG: schedule at 2am; retry 3x; SLA alert if not complete by 6am

---

### Q27
- **Question:** Write a SQL query to compute medical loss ratio (MLR) per plan per month.
- **Tier:** T1
- **Answer:**
  ```sql
  SELECT
      plan_code,
      DATE_TRUNC('month', service_date) AS month,
      SUM(paid_amount) AS total_claims,
      SUM(premium_amount) AS total_premium,
      ROUND(SUM(paid_amount) / NULLIF(SUM(premium_amount), 0), 4) AS mlr
  FROM claims c
  JOIN member_premiums mp USING (member_id, plan_code)
  WHERE service_date >= '2024-01-01'
  GROUP BY plan_code, DATE_TRUNC('month', service_date)
  ORDER BY plan_code, month;
  ```

---

### Q28–Q50 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 28 | Write a query to identify members with > 3 ER visits in 30 days (high utilizers). | SQL | T1 |
| 29 | Write a Pandas pipeline to compute per-member total spend YTD grouped by service type. | Pandas | T1 |
| 30 | How do you implement HEDIS measure calculation in Python? | Healthcare Analytics | T2 |
| 31 | How do you implement a provider scorecard pipeline in Python? | Analytics | T1 |
| 32 | How do you implement a member 360 view (claims + eligibility + pharmacy) in Python? | Data | T1 |
| 33 | How do you process pharmacy claims (NCPDP format) in Python? | Pharmacy | T2 |
| 34 | How do you implement a cohort analysis for members with chronic conditions? | Analytics | T1 |
| 35 | How do you implement a pipeline to compute PMPM (Per Member Per Month) costs? | Analytics | T1 |
| 36 | How do you handle late claims (claims submitted 90+ days after service) in a pipeline? | Claims Logic | T1 |
| 37 | How do you implement readmission rate calculation in Python? | Analytics | T2 |
| 38 | How do you implement a risk stratification pipeline using ML in Python? | ML | T1 |
| 39 | How do you implement a population health analysis dashboard data pipeline? | Analytics | T1 |
| 40 | How do you implement a denial rate analysis by provider and denial reason? | Analytics | T1 |
| 41 | How do you implement a claims editing engine (claim rules validation) in Python? | Claims Logic | T1 |
| 42 | How do you compute the EBM (evidence-based medicine) compliance rate for providers? | Analytics | T2 |
| 43 | How do you implement a pharmacy trend report comparing generic vs brand usage? | Pharmacy | T1 |
| 44 | How do you implement SCD Type 2 for member eligibility history in Python? | Data Engineering | T2 |
| 45 | How do you implement a provider network adequacy analysis in Python? | Analytics | T2 |
| 46 | How do you implement a Python pipeline for CMS 1500 form processing? | Claims | T2 |
| 47 | How do you implement bulk enrollment data processing for open enrollment? | Operations | T1 |
| 48 | How do you implement a member gap-in-care analysis for preventive care? | Analytics | T2 |
| 49 | How do you implement an appeals tracking and aging report pipeline in Python? | Operations | T1 |
| 50 | How do you implement a Python pipeline that reconciles paid claims vs remittance? | Finance | T1 |

---

## SECTION 3: GenAI for Healthcare (Q51–Q75)

### Q51
- **Question:** How would you implement an AI-powered prior authorization assistant?
- **Tier:** T1
- **Answer Points:**
  - **Document ingestion:** PA request form → Azure Document Intelligence → structured data
  - **RAG retrieval:** embed clinical criteria documents (MCG, InterQual); retrieve relevant criteria
  - **LLM extraction:** extract ICD codes, CPT codes, clinical indicators from provider notes
  - **Clinical criteria matching:** LLM compares extracted clinical info vs retrieved criteria
  - **Decision:** if all criteria met → auto-approve; if partially met → medical review; if clearly denied → deny with reason
  - **HITL:** cases with confidence < 0.75 → medical director review queue
  - **Audit:** every decision logged: LLM reasoning, retrieved docs, clinical data used, timestamp, reviewer
  - **HIPAA:** PHI masked before LLM calls; Azure OpenAI for data residency compliance

---

### Q52
- **Question:** How do you build a HIPAA-compliant RAG system for a healthcare chatbot?
- **Tier:** T1
- **Answer Points:**
  - **LLM choice:** Azure OpenAI (HIPAA BAA available); never send PHI to OpenAI without BAA
  - **PHI masking:** presidio-based masking before any text leaves internal systems
  - **Vector store:** Azure AI Search (in Azure tenant, SOC2 + HIPAA compliant)
  - **Access control:** member can only retrieve their own documents; row-level security
  - **Audit log:** every query and response logged with user_id, timestamp, document IDs retrieved
  - **Response guardrails:** never diagnose; always include "consult your doctor" disclaimer; refuse off-topic medical advice
  - **Data retention:** conversation logs retained per HIPAA retention policy (6 years)

---

### Q53–Q75 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 53 | How do you implement automated medical coding (ICD/CPT) using LLMs? | GenAI | T1 |
| 54 | How do you build a clinical notes summarization pipeline for care managers? | GenAI | T1 |
| 55 | How do you implement a drug interaction checking agent for pharmacy? | GenAI | T1 |
| 56 | How do you build a member health risk assessment chatbot? | GenAI | T1 |
| 57 | How do you implement a claims fraud detection system using ML + LLMs? | GenAI | T1 |
| 58 | How do you implement a FHIR-aware RAG pipeline for clinical decision support? | GenAI | T1 |
| 59 | How do you implement automated denial letter generation from structured data? | GenAI | T1 |
| 60 | How do you implement a provider directory Q&A bot using RAG? | GenAI | T1 |
| 61 | How do you implement a member eligibility Q&A agent with tool calling? | GenAI | T1 |
| 62 | How do you implement clinical note de-identification using Python? | HIPAA | T1 |
| 63 | How do you implement an AI-powered EOB (Explanation of Benefits) generator? | GenAI | T1 |
| 64 | How do you implement a clinical trial eligibility screening agent? | GenAI | T2 |
| 65 | How do you implement a discharge planning recommendation system with LLMs? | GenAI | T2 |
| 66 | How do you implement a care gap identification agent using member data? | GenAI | T1 |
| 67 | How do you implement a medication adherence monitoring system with AI? | GenAI | T2 |
| 68 | How do you evaluate a clinical AI model for bias across demographic groups? | AI Ethics | T1 |
| 69 | How do you implement a patient triage chatbot that routes to appropriate care? | GenAI | T1 |
| 70 | How do you implement a regulatory compliance monitoring agent for CMS rules? | GenAI | T1 |
| 71 | How do you implement a HEDIS measure gap closure recommendation system? | GenAI | T2 |
| 72 | How do you implement a provider credentialing assistant with document AI? | GenAI | T1 |
| 73 | How do you implement a Python agent for prior authorization appeals? | GenAI | T1 |
| 74 | How do you implement a health plan benefit design analyzer using LLMs? | GenAI | T2 |
| 75 | How do you implement a clinical outcome prediction model with explainability? | ML | T1 |

---

## SECTION 4: Healthcare System Design (Q76–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 76 | Design a HIPAA-compliant claims processing API: intake, validation, adjudication, payment. | System Design | T1 |
| 77 | Design a prior authorization platform with auto-adjudication, HITL, and audit trail. | System Design | T1 |
| 78 | Design a real-time eligibility verification service that handles 100K req/hour. | System Design | T1 |
| 79 | Design a healthcare data lake that supports both analytics and AI workloads. | Architecture | T1 |
| 80 | Design a member 360 API that aggregates claims, eligibility, pharmacy, and clinical data. | Architecture | T1 |
| 81 | Design a FHIR R4 API for a health insurance company with role-based data access. | Architecture | T1 |
| 82 | Design a fraud detection system for healthcare claims using Python ML + rules engine. | ML | T1 |
| 83 | Design a population health management platform for a Medicare Advantage plan. | Architecture | T1 |
| 84 | Design a real-time provider data management system for a national insurer. | Architecture | T1 |
| 85 | Design a clinical decision support system for care managers using GenAI. | GenAI | T1 |
| 86 | Design a Python-based risk adjustment pipeline for a CMS EDGE server submission. | Analytics | T2 |
| 87 | Design an audit logging system that meets HIPAA audit control requirements. | Compliance | T1 |
| 88 | Design a member communication platform for claims status and benefit information. | Architecture | T1 |
| 89 | Design a Python service for near-real-time claim status updates to providers. | Architecture | T1 |
| 90 | Design a ML pipeline for predicting hospital readmissions from claims data. | ML | T1 |
| 91 | Design a Python ETL pipeline to process 10M claims records monthly from multiple payers. | Data Engineering | T1 |
| 92 | Design a HIPAA-compliant chatbot for member benefit inquiries using RAG. | GenAI | T1 |
| 93 | Design a Python-based appeals management system with workflow automation. | Architecture | T1 |
| 94 | Design a claims editing engine that applies 500+ business rules to each claim. | Architecture | T1 |
| 95 | How do you implement disaster recovery for a HIPAA-regulated Python service? | Compliance | T1 |
| 96 | How do you handle PHI in a multi-cloud environment (AWS + Azure)? | Compliance | T1 |
| 97 | How do you implement a Business Associate Agreement (BAA) workflow with cloud vendors? | Compliance | T1 |
| 98 | How do you implement CMS Star Quality ratings data processing in Python? | Analytics | T2 |
| 99 | How do you implement a Python service that integrates with Epic/Cerner FHIR APIs? | Integration | T1 |
| 100 | Design a complete GenAI platform for a healthcare insurance company: claims automation, prior auth AI, member chatbot, provider analytics — fully HIPAA-compliant, SOC2 certified, and at scale. | Architecture | T1 |
