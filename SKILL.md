---
name: silicon-errata-auditor
description: Audit embedded firmware and hardware configurations against vendor silicon errata. Use when determining whether MCU, SoC, FPGA, or peripheral errata apply to a device or silicon revision; tracing affected implementation or configuration; validating workarounds; producing evidence-linked findings or compliance reports; or designing laboratory tests for silicon-specific behavior. Do not use for general firmware review, ordinary performance work, or generic board bring-up unless a silicon erratum or suspected silicon-specific behavior is in scope.
---

# Silicon Errata Auditor

Audit the supplied project against authoritative silicon errata and preserve an evidence chain for every conclusion.

## Start the audit

1. Read [references/source-policy.md](references/source-policy.md) before selecting or citing sources.
2. Read [references/report-format.md](references/report-format.md) before drafting the deliverable.
3. Consult [references/investigation-playbook.md](references/investigation-playbook.md) when locating errata documents or searching the project.
4. Consult [references/severity-rubric.md](references/severity-rubric.md) when scoring the severity of any finding.
5. Inventory the available device evidence, vendor documents, firmware, generated configuration, hardware files, build artifacts, and test records.
6. State the audit scope and distinguish verified facts, user-provided claims, engineering inferences, unknowns, unverified document currency, and required follow-up evidence.
7. Ask for missing information only when continuing would make the result materially unreliable. Otherwise, perform a clearly scoped preliminary analysis.

## Establish device identity

Record the most exact identity available:

- Manufacturer
- Product family
- Complete ordering code
- Package
- Die or silicon revision
- Board revision
- Relevant peripheral revisions

Never silently assume a revision. Record an unavailable field as unknown and analyze all plausibly applicable revisions.

When revision evidence is missing, request or recommend evidence from identification registers, boot logs, package markings, programmer or debugger output, manufacturing records, or board documentation. Do not invent a device-specific register address or register-reading procedure; require an authoritative vendor source before giving one.

## Build the errata set

1. Locate official errata for the exact product and revision using the vendor-behavior authority guidance.
2. Verify document currency from an official vendor source when network access permits. Otherwise, label currency unverified.
3. Record document identifiers, revisions, publication dates, section numbers, erratum IDs, and page numbers when available.
4. Include every entry that applies or could plausibly apply to an unknown revision. Record explicit scope exclusions.
5. Extract for each entry:
   - Erratum ID and title
   - Affected products and silicon revisions
   - Affected peripheral or subsystem
   - Triggering conditions
   - Observable failure or consequence
   - Official workaround and its limitations
   - Source citation
   - Ambiguities or missing information
6. Keep vendor statements separate from engineering interpretation. Never elevate forum posts, copied blog content, or model memory to authoritative evidence.

## Inspect the project

Search all relevant artifacts, including:

- C, C++, Rust, assembly, startup, and linker files
- Register-level code and HAL, LL, SDK, BSP, or RTOS configuration
- Devicetree, overlays, Kconfig, and generated configuration
- STM32CubeMX `.ioc`, clock-tree, PLL, DMA, interrupt, cache, memory, and low-power configuration
- Peripheral modes, wake-up paths, bootloaders, and startup code
- Schematics, nets, component options, FPGA constraints, and generated hardware configuration
- CI checks, build artifacts, tool reports, logs, and hardware test procedures

Use precise paths, symbols, configuration names, and line references. Trace configuration through build inclusion, initialization, call sites, affected runtime paths, and available runtime evidence. Do not infer that a peripheral is used merely because its driver exists. Treat generated files carefully: identify their source configuration and whether regeneration could remove a workaround.

Read the erratum's triggering condition first, then search for the specific conditions it names. [references/investigation-playbook.md](references/investigation-playbook.md) gives per-subsystem search patterns, the positive-use chain that establishes a peripheral is actually active, and the generated-configuration traps that silently remove workarounds.

## Classify applicability

The default when evidence runs out is an unresolved status, never Not applicable.

```dot
digraph classify {
    "Vendor scope excludes this device/revision?" [shape=diamond];
    "Positive evidence trigger cannot occur?" [shape=diamond];
    "Affected identity and trigger path evidenced?" [shape=diamond];
    "Can documentary/static evidence resolve the unknown?" [shape=diamond];
    "Official workaround verified on every affected path?" [shape=diamond];
    "Workaround absence or defect positively evidenced?" [shape=diamond];
    "Not applicable" [shape=box];
    "Potentially applicable" [shape=box];
    "Requires lab verification" [shape=box];
    "Confirmed exposure" [shape=box];
    "Mitigated" [shape=box];

    "Vendor scope excludes this device/revision?" -> "Not applicable" [label="yes, authoritative"];
    "Vendor scope excludes this device/revision?" -> "Positive evidence trigger cannot occur?" [label="no or unknown revision"];
    "Positive evidence trigger cannot occur?" -> "Not applicable" [label="yes, positive evidence"];
    "Positive evidence trigger cannot occur?" -> "Affected identity and trigger path evidenced?" [label="no or unknown"];
    "Affected identity and trigger path evidenced?" -> "Can documentary/static evidence resolve the unknown?" [label="no"];
    "Can documentary/static evidence resolve the unknown?" -> "Potentially applicable" [label="yes, evidence pending"];
    "Can documentary/static evidence resolve the unknown?" -> "Requires lab verification" [label="no, inherently physical/runtime"];
    "Affected identity and trigger path evidenced?" -> "Official workaround verified on every affected path?" [label="yes"];
    "Official workaround verified on every affected path?" -> "Mitigated" [label="yes"];
    "Official workaround verified on every affected path?" -> "Workaround absence or defect positively evidenced?" [label="no"];
    "Workaround absence or defect positively evidenced?" -> "Confirmed exposure" [label="yes"];
    "Workaround absence or defect positively evidenced?" -> "Potentially applicable" [label="no, mitigation unverified"];
}
```

Assign every analyzed erratum exactly one status:

- **Not applicable**: Authoritative device or revision scope excludes it, or positive project evidence proves a required trigger cannot occur in the audited configuration. Do not use missing code alone as proof.
- **Potentially applicable**: Applicability, trigger exposure, or complete mitigation remains unresolved, and documentary or static evidence could resolve it. This includes an evidenced trigger when the available artifacts merely fail to prove whether the workaround is complete.
- **Confirmed exposure**: The affected identity and triggering path are evidenced, and positive project evidence shows the required workaround is absent, incomplete, incorrectly timed, or invalidated.
- **Mitigated**: The affected identity and triggering path are evidenced, and the official workaround is verified on every relevant path with its limitations acknowledged.
- **Requires lab verification**: An unresolved condition inherently depends on physical, timing, analog, environmental, load, or runtime behavior that documentary and static evidence cannot decide.

For every status, cite both device/erratum scope evidence and project evidence. Never treat missing code, an unsuccessful search, or any other absence of evidence as proof of non-use or workaround absence.

## Assess severity

Keep the vendor's severity, disposition, impact category, or product-status label separate from project severity. Preserve the vendor value verbatim with its exact document location, or record "Unknown" when the vendor supplies none. Do not assume the vendor scale maps ordinally to Critical, High, Medium, or Low.

For resolved applicable findings, derive project severity from Impact and the assessed Likelihood supported by project evidence. For **Potentially applicable** and **Requires lab verification** findings, record both the evidence-supported likelihood and a separate uncertainty ceiling; derive a provisional worst-case severity from Impact and that disclosed ceiling, and name the evidence needed to resolve it. Never present the ceiling as an observed exposure rate. For **Not applicable**, record project severity as "Not applicable" with the excluding evidence rather than assigning Critical through Low.

Consult [references/severity-rubric.md](references/severity-rubric.md) for the scoring model, notation, and vendor/project separation.

## Validate a workaround

Do not accept a comment, symbol name, or vendor-library presence as proof of mitigation. Verify that:

- The implementation matches the official workaround without invented requirements.
- It executes on the affected path and at the required time.
- Build options and compiler optimization cannot remove or reorder critical behavior.
- Concurrency, interrupts, DMA, caches, memory ordering, and power transitions do not invalidate it.
- It applies to the exact silicon revision.
- All vendor-stated limitations and side effects are acknowledged.

Downgrade the conclusion to the appropriate unresolved status when any required property lacks evidence.

## Recommend changes

For confirmed or potential exposure:

1. Explain the failure mechanism and cite the authoritative erratum.
2. Identify the affected code or configuration.
3. Propose the smallest reasonable mitigation.
4. Separate required changes from optional defensive improvements.
5. Explain effects on latency, throughput, power, memory, reliability, or peripheral capabilities.
6. Provide a patch only when the evidence is sufficient.
7. Mark unverified code suggestions clearly.

Never fabricate register names, bit positions, timing values, electrical limits, or vendor requirements.

## Design laboratory verification

When inspection cannot establish applicability, specify:

- Hypothesis
- Required equipment
- Firmware instrumentation
- Stimulus and operating conditions
- Relevant voltage, temperature, clock, load, and timing corners
- Measurement points
- Expected normal behavior and expected failure signature
- Repetitions or duration
- Objective pass/fail criteria
- Evidence to archive

Recommend a debugger, logic analyzer, oscilloscope, power analyzer, environmental chamber, or other equipment only when it is relevant. Tie every test condition to vendor evidence or label it as an engineering choice.

## Produce the report

Choose the proportional deliverable defined in [references/report-format.md](references/report-format.md):

- Use a **full audit report** for an entire device, subsystem, project, or errata document; for multiple errata; for compliance or sign-off; or when the user requests a complete report. Include every erratum in the stated scope in the applicability matrix.
- Use a **focused finding** for one erratum, workaround, peripheral exposure, revision question, or laboratory-verification question. Preserve every evidence-chain field required by the focused template without emitting unrelated full-report sections; record a required field as "Unknown" rather than omit it.
- When critical inputs are missing, provide a clearly labeled preliminary analysis and keep scope limitations and required evidence prominent.

For either mode, ensure that:

- Before returning the deliverable, audit it field by field against the selected format. For a focused finding, preserve the template's 11 bold field labels verbatim; do not merge vendor and project severity or fold verification and unknowns into other fields. Record "Unknown" when a required value cannot be established.
- Every detailed finding traces a vendor statement to device evidence, project evidence, analysis, action, and verification.
- Citations identify exact documents and locations where available.
- Vendor severity/category and project-derived severity remain distinct.
- Project severity follows [references/severity-rubric.md](references/severity-rubric.md), including "Not applicable" and provisional uncertainty-ceiling notation.
- Confidence reflects evidence quality rather than rhetorical certainty.
- Unknowns and required inputs remain visible.
- The report makes no claim that document currency was verified unless an official source was actually checked.

## Red flags

Each of these means the conclusion is over-claimed. Downgrade the status, lower the confidence, and record the required follow-up evidence.

| Reasoning that appears | What it actually establishes |
|---|---|
| "No call site found, so the peripheral is unused." | Nothing. Search coverage is not proof of non-use. Establish the positive use chain instead. |
| "The device is probably a recent revision." | Nothing. An unknown revision means analyzing every plausibly applicable revision. |
| "The SDK is recent, so the workaround is included." | Nothing. Locate the workaround in the built image and confirm it runs on the affected path. |
| "A comment or function name references the erratum." | Intent, not correctness, timing, or coverage. |
| "The sibling part has the same erratum." | Nothing without explicit vendor scope covering this part. |
| "This is the latest errata document." | Only true if an official index or product page was actually checked. Otherwise currency is unverified. |
| "I recall this erratum affects..." | A discovery lead only. Model memory is never an authoritative citation. |
| "The workaround is in the source tree." | Nothing about build inclusion, optimization survival, or regeneration. |

Fabricating a register name, address, bit position, timing value, electrical limit, or affected-revision list is never acceptable, even to complete a template field. Record it as unknown.
