# Silicon Errata Auditor

An Agent Skill for auditing embedded firmware and hardware configuration against vendor-published silicon errata. It helps identify applicable errata for an exact device revision, trace triggering conditions through project artifacts, validate official workarounds, identify laboratory-only questions, and produce an evidence-linked report.

## What it does

- Establishes device, package, silicon, board, and peripheral revision identity without silently guessing.
- Applies an explicit hierarchy for vendor documents and project evidence.
- Inspects firmware, generated configuration, build artifacts, and hardware documentation.
- Classifies each erratum as Not applicable, Potentially applicable, Confirmed exposure, Mitigated, or Requires lab verification.
- Produces traceable recommendations and practical laboratory verification plans.

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

- ?Audit this STM32H743 firmware against the applicable silicon errata.?
- ?Determine which ESP32-S3 errata affect this repository and whether the official workarounds are implemented.?
- ?I do not know the silicon revision. Perform a preliminary audit and tell me how to identify it.?
- ?Create a lab verification plan for the potentially applicable Ethernet and DMA errata.?
- ?Review this existing workaround and determine whether it fully satisfies the vendor erratum.?

## Repository structure

```text
silicon-errata-auditor/
??? SKILL.md
??? agents/
?   ??? openai.yaml
??? references/
?   ??? report-format.md
?   ??? source-policy.md
??? README.md
```

## Installation

```sh
npx skills add https://github.com/Dheerajsom/silicon-errata-auditor
```

If discovery needs an explicit selection:

```sh
npx skills add https://github.com/Dheerajsom/silicon-errata-auditor --skill silicon-errata-auditor
```

## Safety and evidence

Treat the output as an engineering audit aid, not a substitute for official vendor documentation or qualified safety review. Verify document currency, device revision, patches, and laboratory conclusions before relying on them in production or safety-critical systems.
