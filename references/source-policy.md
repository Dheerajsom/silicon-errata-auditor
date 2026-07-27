# Source and Evidence Policy

Apply this policy before researching errata, interpreting device scope, or making an applicability claim.

## Evidence hierarchy

Prefer evidence in this order:

1. Official vendor errata for the exact device and revision
2. Official reference manual and datasheet
3. Official application notes or vendor knowledge-base articles
4. User-provided schematic, configuration, and firmware
5. Tool-generated reports and build artifacts
6. Clearly labeled engineering inference

Use lower-ranked evidence to supplement, not silently override, higher-ranked evidence. Treat forum posts, copied blog content, distributor summaries, and model memory as discovery leads only. Do not cite them as authoritative errata requirements.

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
