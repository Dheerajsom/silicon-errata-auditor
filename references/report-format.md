# Silicon Errata Deliverable Formats

Choose the smallest format that preserves the complete evidence chain.

## Select the mode

Use a **full audit report** when auditing an entire device, subsystem, project, or errata document; comparing multiple errata; producing a compliance or sign-off artifact; or responding to an explicit request for a complete report. Its applicability matrix must contain every erratum in the stated scope.

Use a **focused finding** when the request concerns one erratum, workaround, peripheral exposure, revision question, or laboratory-verification question. Do not emit empty sections from the full report merely for template compliance.

When critical inputs are missing, issue a clearly labeled **Preliminary analysis** in either mode. Keep scope limitations, unresolved classifications, confidence, and required follow-up evidence prominent.

## Focused finding format

Use every field in this concise structure and preserve the bold labels verbatim. Replace each instruction with evidence or an explicit Unknown; do not omit an evidence-chain field merely because its input is missing. Keep Vendor severity/category and Project severity separate, and do not fold Verification procedure or Unknowns and required follow-up evidence into another field. Before returning the finding, verify that all 11 bold fields below are present.

### Focused finding: _erratum ID and title or question_

- **Scope and device/revision identity:** _State the audited artifact, target, exact identity evidence, plausible revisions, limitations, and confidence._
- **Erratum and vendor citation:** _State scope, trigger, consequence, workaround, limitations, and exact authoritative document location._
- **Vendor severity/category:** _Preserve the vendor's verbatim severity, disposition, impact category, or product-status label with its exact citation; otherwise state Unknown._
- **Project evidence:** _Cite inspected paths, lines, symbols, build artifacts, configuration, hardware records, logs, or laboratory data._
- **Classification:** _Use exactly one allowed status and state its evidence basis._
- **Project severity:** _For a resolved applicable finding, state Critical, High, Medium, or Low derived from Impact and assessed Likelihood. For an unresolved finding, state provisional severity, supported exposure, uncertainty ceiling, and pending evidence. For Not applicable, state Not applicable and cite the excluding evidence._
- **Analysis:** _Connect the vendor condition to project evidence and label verified facts, user claims, engineering inferences, contradictions, and unknowns._
- **Existing mitigation:** _State verified implementation and path coverage, positively evidenced absence or defect, or Unknown when completeness is merely unverified._
- **Recommended action:** _Give the smallest justified change and distinguish required mitigation from optional defense._
- **Verification procedure:** _Define static, build, runtime, or laboratory checks with objective pass/fail criteria._
- **Unknowns and required follow-up evidence:** _Name each unresolved input, how to obtain it safely, and how it could change the conclusion._

## Full audit report format

Use the complete structure below. Replace instructional italic text with project-specific evidence, or retain it as an explicit unknown when the information is unavailable.

# Silicon Errata Applicability Report

**Project:** _Identify the audited project and build or commit._

**Audit date:** _Give the date._

**Audit scope:** _State included devices, revisions, firmware targets, hardware revisions, documents, and exclusions._

**Document currency:** _State verified or unverified and cite the official currency check when performed._

## 1. Executive summary

_Summarize device identity and counts by classification. Summarize Critical, High, Medium, and Low project severity for applicable and unresolved findings, identify provisional values, and list Not applicable findings separately without a Critical–Low score. Highlight highest-risk exposures, verified mitigations, laboratory work, decisions, unknown revisions, and document-currency limitations._

## 2. Device identity and revision evidence

| Identity field | Value | Evidence | Evidence label | Confidence |
|---|---|---|---|---|
| Manufacturer | _State or unknown_ | _Cite evidence_ | _Verified fact, user-provided claim, inference, or unknown_ | _High, medium, or low_ |
| Product family | _State or unknown_ | _Cite evidence_ | _Label_ | _Confidence_ |
| Complete ordering code | _State or unknown_ | _Cite evidence_ | _Label_ | _Confidence_ |
| Package | _State or unknown_ | _Cite evidence_ | _Label_ | _Confidence_ |
| Die/silicon revision | _State or unknown_ | _Cite evidence_ | _Label_ | _Confidence_ |
| Board revision | _State or unknown_ | _Cite evidence_ | _Label_ | _Confidence_ |
| Peripheral revision | _State, not relevant, or unknown_ | _Cite evidence_ | _Label_ | _Confidence_ |

_If identity is incomplete, list every plausibly applicable revision and explain how to obtain authoritative evidence._

## 3. Documents and project artifacts reviewed

### Vendor and external documents

| Source | Identifier | Revision/date | Exact locations used | Official source/currency check | Notes |
|---|---|---|---|---|---|
| _Document title_ | _Document ID_ | _Revision and date_ | _Erratum, section, and pages_ | _Official URL and access date, or unverified_ | _Scope or limitation_ |

### Project artifacts

| Artifact | Version/target | Locations reviewed | Relevance | Evidence limitations |
|---|---|---|---|---|
| _Repository file, schematic, build, log, or report_ | _Commit/build/board_ | _Paths, lines, symbols, sheets, or fields_ | _Trigger, workaround, or identity evidence_ | _Missing runtime or generated-state caveat_ |

## 4. Applicability matrix

| Erratum / vendor scope | Project evidence | Status | Vendor severity/category | Project severity | Workaround | Source |
|---|---|---|---|---|---|---|
| _ID, title, affected revisions_ | _Concise artifact citations_ | _Exactly one allowed classification_ | _Verbatim vendor label, or Unknown_ | _Resolved score, provisional score with supported exposure and ceiling, or Not applicable_ | _Positively evidenced absent/defective, verified, not required, or unknown_ | _Exact vendor citation_ |

Use exactly one status per row: Not applicable, Potentially applicable, Confirmed exposure, Mitigated, or Requires lab verification.

Keep the two severity fields distinct. Preserve the vendor category verbatim and cite its exact location; record Unknown when none is supplied. Do not directly compare it with project severity unless an authoritative mapping or demonstrated equivalence permits comparison.

Every row carries a project-severity value:

- For a resolved applicable finding, derive Critical, High, Medium, or Low from Impact and assessed Likelihood.
- For Potentially applicable or Requires lab verification, label the result provisional, show Impact, supported exposure, and the uncertainty ceiling used for the worst-case matrix result, and name the evidence that would resolve it.
- For Not applicable, record **Not applicable** and cite the excluding evidence; do not assign Critical through Low.

Record a workaround as absent, incomplete, incorrectly timed, or invalidated only when positive project evidence supports that state. If artifacts merely fail to prove completeness, record the workaround as unknown and keep the classification unresolved.

## 5. Detailed findings

Repeat this subsection for every analyzed erratum.

### Finding _stable sequential ID_: _erratum ID and title_

- **Finding ID:** _Stable report identifier_
- **Erratum ID:** _Vendor identifier_
- **Classification:** _Exactly one allowed status_
- **Vendor severity/category:** _Preserve the vendor's verbatim severity, disposition, impact category, or product-status label with its exact document location; otherwise state Unknown. Do not silently translate it._
- **Project severity:** _For a resolved applicable finding, state Critical, High, Medium, or Low with Impact and assessed Likelihood. For an unresolved finding, state provisional worst-case severity with Impact, supported exposure, uncertainty ceiling, and pending evidence. For Not applicable, state Not applicable with excluding evidence._
- **Assessed likelihood/exposure:** _State the highest level supported by affirmative project evidence, or Not applicable._
- **Uncertainty ceiling:** _For an unresolved finding, state the highest plausible level not ruled out and never describe it as observed; otherwise state not used._
- **Confidence:** _High, medium, or low, with a brief evidence-based reason_
- **Vendor statement:** _Concise quotation or faithful paraphrase of affected scope, trigger, consequence, workaround, and limitations; separate vendor language from interpretation_
- **Project evidence:** _Cite identity and project paths, lines, symbols, configuration fields, logs, schematic locations, or state that the required evidence is unknown_
- **Analysis:** _Connect vendor conditions to project evidence; identify verified facts, user-provided claims, inferences, contradictions, and unknowns_
- **Existing mitigation:** _Describe verified implementation and execution-path evidence; state an absent or defective workaround only with positive evidence; otherwise state unknown_
- **Recommended action:** _Give the smallest required mitigation, optional defensive improvements, side effects, and any unverified suggestion label_
- **Verification procedure:** _Specify static, build, runtime, or laboratory confirmation with objective criteria_
- **Unknowns and required follow-up evidence:** _Name evidence needed to resolve applicability, trigger exposure, workaround completeness, assessed likelihood, or the uncertainty ceiling_
- **Source citations:** _List exact vendor and project locators_

## 6. Recommended changes

### Required changes

| Priority | Finding | Change | Location | Rationale/source | Side effects | Verification |
|---|---|---|---|---|---|---|
| _Priority_ | _Finding ID_ | _Smallest required change_ | _Exact artifact location_ | _Erratum citation_ | _Latency, throughput, power, memory, or capability effect_ | _Objective check_ |

### Optional defensive improvements

| Finding | Improvement | Benefit | Cost or risk | Verification |
|---|---|---|---|---|
| _Finding ID_ | _Optional improvement_ | _Expected benefit_ | _Tradeoff_ | _Check_ |

_Mark all code or configuration suggestions that have not been tested. Do not supply a patch when authoritative details are missing._

## 7. Laboratory verification plan

Repeat the following for each finding classified Requires lab verification, and for any other finding needing physical confirmation.

### Lab test _finding ID_: _short name_

- **Hypothesis:** _State the condition and predicted behavior._
- **Required equipment:** _Name only relevant instruments, fixtures, and calibrated capabilities._
- **Firmware instrumentation:** _Define counters, timestamps, GPIO markers, logging, trace, or fault capture._
- **Stimulus and operating conditions:** _Define workload and sequence._
- **Corners:** _Define voltage, temperature, clock, load, and timing corners supported by vendor evidence or clearly labeled engineering choices._
- **Measurement points:** _Identify signals, rails, registers, logs, or outputs._
- **Expected normal behavior:** _Define the control outcome._
- **Expected failure signature:** _Define the observable erratum manifestation._
- **Repetitions or duration:** _Give a justified count, duration, or statistical target._
- **Pass/fail criteria:** _State objective thresholds and decision rules._
- **Evidence to archive:** _List firmware/build identity, device markings, setup, instrument files, raw captures, logs, conditions, and results._

## 8. Unknowns and required inputs

| Unknown or limitation | Impact on conclusions | Required evidence | How to obtain it | Affected findings |
|---|---|---|---|---|
| _Unknown_ | _Scope, confidence, or classification impact_ | _Specific artifact or observation_ | _Safe evidence-gathering method_ | _Finding IDs_ |

_Include unresolved device revision, peripheral revision, document currency, build configuration, hardware state, and runtime behavior. Never convert a missing artifact into a not-applicable conclusion._

## 9. Traceability appendix

| Finding | Device evidence | Vendor requirement | Project evidence | Classification basis | Action | Verification evidence |
|---|---|---|---|---|---|---|
| _Finding ID_ | _Citation_ | _Erratum and exact location_ | _Artifact citation_ | _Brief rule/evidence threshold_ | _Required action or none_ | _Existing evidence or pending test_ |

### Evidence legend

- **Verified fact:** Directly supported by an authoritative document or inspected artifact.
- **User-provided claim:** Supplied but not independently verified.
- **Engineering inference:** Reasoned from cited facts.
- **Unknown:** Not established by available evidence.
- **Unverified document currency:** Official latest-revision status was not checked.
- **Required follow-up evidence:** Specific evidence needed to resolve a conclusion.
