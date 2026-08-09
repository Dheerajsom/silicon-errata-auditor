# Source and Evidence Policy

Apply this policy before researching errata, interpreting device scope, or making an applicability claim.

## Authority by claim type

Select authority according to the claim being established. Do not impose one universal ordering across vendor behavior, device identity, and project state.

### Vendor silicon behavior and erratum scope

Prefer:

1. Official errata for the exact product and revision
2. Official reference manual and datasheet
3. Official application notes
4. Official vendor knowledge-base material
5. Non-authoritative sources as discovery leads only

Only official, revision-appropriate vendor evidence may establish affected revisions, triggering conditions, consequences, or required workarounds. Treat forum posts, copied blog content, distributor summaries, and model memory only as leads for locating official material.

### Device identity and revision

Use directly observed identity evidence and interpret it through authoritative vendor documentation. Candidate evidence includes:

- Identification registers
- Programmer or debugger output
- Boot or ROM logs
- Package or mask markings
- Manufacturing and traceability records
- Board documentation

Record provenance, the authoritative interpretation source, and confidence. Do not let an undocumented observation silently establish a revision.

### Project implementation and configuration

Treat inspected project artifacts as primary evidence of the project's actual state. Choose evidence based on the claim and prefer evidence closest to the shipped or executed configuration:

- Runtime captures, register dumps, trace, and test records
- Built binaries, disassembly, linker maps, and build reports
- Exact shipping build configuration
- Generated configuration together with its tracked source
- Handwritten source, hardware files, and schematics

Do not turn this list into a rigid ordering. Source may establish intent, while a built image may better establish inclusion and runtime evidence may better establish execution. Reconcile contradictions explicitly and state which artifact answers the claim most directly.

### User claims and engineering inference

Keep user-provided claims and engineering inferences explicitly labeled. Use them to guide investigation, but never let them silently replace authoritative vendor evidence for silicon behavior or directly inspected evidence for project state.

## Citation requirements

For vendor documents, capture every available locator:

- Manufacturer and document title
- Document identifier
- Revision or version
- Publication or update date
- Erratum ID
- Section or table
- Page number
- Official URL or user-provided filename
- Access date when retrieved online

For project evidence, cite the repository-relative file path and exact line, symbol, configuration key, schematic sheet/net, build target, log location, or tool-report field. State when generated evidence may depend on a particular build.

For each conclusion, preserve this chain:

1. Device or revision evidence
2. Vendor scope and triggering condition
3. Project or laboratory evidence
4. Engineering analysis
5. Classification and confidence

Quote only the minimum vendor text needed to avoid ambiguity. Clearly label paraphrases and engineering interpretations.

## Document currency

Never call an errata document the latest or current revision unless an official vendor index, product page, document history, or equivalent source has been checked.

When network access is unavailable or the vendor source cannot be reached:

- Use the supplied documents for a scoped audit.
- Record their identifiers and revisions.
- Mark document currency as unverified.
- Request a later official-currency check as follow-up.

Do not discard an older document when it is the only source supplied; report the limitation.

## Revision handling

Never silently infer die revision from product family, board age, firmware target, SDK version, or package appearance.

Accept revision evidence from authoritative or directly observed sources such as:

- Identification registers interpreted with official documentation
- Boot or ROM logs whose fields are vendor-documented
- Package markings mapped through official documentation
- Programmer or debugger identification output
- Manufacturing and traceability records
- Board and assembly documentation

Record the evidence source and confidence. If the revision is unknown, analyze all plausibly applicable revisions and show how conclusions vary by revision.

Do not recommend reading a device-specific address until an authoritative source establishes the register, address, access method, field encoding, and any safety constraints.

## Evidence labels

Label material statements with one of these evidence types:

- **Verified fact**: Directly supported by an authoritative document or inspected artifact.
- **User-provided claim**: Stated by the user but not independently verified.
- **Engineering inference**: Reasoned from cited facts; explain the reasoning.
- **Unknown**: Evidence is absent or contradictory.
- **Unverified document currency**: The document is usable, but official latest-revision status was not checked.
- **Required follow-up evidence**: A specific artifact or observation needed to resolve a conclusion.

Resolve contradictions explicitly. Prefer the more authoritative and revision-specific source, but do not hide the conflict.

## Prohibited assumptions

Do not:

- Invent affected revisions, register names, addresses, fields, bit positions, timing values, or electrical limits.
- Infer peripheral use merely from the presence of a driver.
- Infer non-use merely because a textual search found no call site.
- Treat a comment, function name, SDK version, or vendor library as proof that a workaround is correct.
- Treat missing evidence as proof that a triggering condition cannot occur.
- Generalize an erratum across sibling products without explicit vendor scope.
- Assume a workaround for one revision applies to another.
- Claim laboratory behavior from static inspection alone.
- Present engineering inference as a vendor statement.

When evidence is insufficient, narrow the claim, lower confidence, choose an unresolved classification, and state the exact follow-up needed.
