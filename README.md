# Silicon Errata Auditor

An Agent Skill for auditing embedded firmware and hardware configuration against vendor-published silicon errata. It helps identify applicable errata for an exact device revision, trace triggering conditions through project artifacts, validate official workarounds, identify laboratory-only questions, and produce an evidence-linked focused finding or full report.

## What it does

- Establishes device, package, silicon, board, and peripheral revision identity without silently guessing.
- Applies claim-specific authority rules for vendor silicon behavior, directly observed device identity, project implementation, and engineering inference.
- Inspects firmware, generated configuration, build artifacts, and hardware documentation, using per-subsystem search patterns to map a triggering condition to the artifacts that carry it.
- Classifies each erratum as Not applicable, Potentially applicable, Confirmed exposure, Mitigated, or Requires lab verification, requiring positive evidence of an absent or defective workaround before declaring Confirmed exposure.
- Keeps a merely unverified workaround unresolved and never treats a failed search as proof of non-use.
- Preserves vendor severity or disposition verbatim while deriving project severity independently from Impact × Likelihood.
- Records evidence-supported likelihood separately from an unresolved finding's uncertainty ceiling and exposes both in provisional severity.
- Checks whether a workaround survives the build: optimization, conditional compilation, link-time removal, revision gating, and configuration regeneration.
- Produces a complete audit report for broad or sign-off work and a concise, evidence-complete finding for a single erratum or question.
- Records project severity as Not applicable, with excluding evidence, when the classification is Not applicable.

## What it does not do

- Guarantee access to vendor portals or proprietary documents.
- Claim support for every vendor, device, or document format.
- Replace vendor guidance, engineering review, safety analysis, or physical validation.
- Invent undocumented register details, timing requirements, or silicon behavior.

## Required inputs

Provide as much of the following as possible:

- Manufacturer, complete ordering code, package, silicon revision, board revision, and relevant peripheral revisions
- Official errata, reference manuals, datasheets, and application notes, or permission to retrieve public versions
- Firmware source and its exact build configuration
- Generated configuration, schematics, FPGA constraints, boot logs, debugger output, and test records when relevant

The skill can perform a preliminary audit with missing inputs, but it will keep unknowns explicit and analyze all plausibly applicable revisions.

## Example prompts

- "Audit this STM32H743 firmware against the applicable silicon errata."
- "Determine which ESP32-S3 errata affect this repository and whether the official workarounds are implemented."
- "I do not know the silicon revision. Perform a preliminary audit and tell me how to identify it."
- "Create a lab verification plan for the potentially applicable Ethernet and DMA errata."
- "Review this existing workaround and determine whether it fully satisfies the vendor erratum."
- "Give me a focused finding for erratum ES1234, including the evidence still needed."

## Repository structure

```text
silicon-errata-auditor/
  SKILL.md                             Audit workflow
  agents/
    openai.yaml                        Agent interface metadata
  references/
    source-policy.md                   Claim-specific authority, citation, revision handling
    report-format.md                   Report template
    severity-rubric.md                 Project severity, assessed likelihood, uncertainty ceilings
    investigation-playbook.md          Errata-class search patterns and doc-locating heuristics
  README.md
```

## Installation

```sh
npx skills add https://github.com/Dheerajsom/silicon-errata-auditor
```

If discovery needs an explicit selection:

```sh
npx skills add https://github.com/Dheerajsom/silicon-errata-auditor --skill silicon-errata-auditor
```

To install manually, copy the repository into your agent's skills directory so that `SKILL.md` sits at `silicon-errata-auditor/SKILL.md` — for Claude Code, `~/.claude/skills/` for personal use or `.claude/skills/` inside a project. The `references/` directory must travel with `SKILL.md`; the skill loads those files on demand during an audit.

## Safety and evidence

Treat the output as an engineering audit aid, not a substitute for official vendor documentation or qualified safety review. Verify document currency, device revision, patches, and laboratory conclusions before relying on them in production or safety-critical systems.
