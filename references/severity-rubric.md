# Severity Rubric

Derive the Severity field in the applicability matrix and in each detailed finding from this rubric. Score two independent dimensions, record both inputs, then read the combined severity out of the matrix.

Severity is a prioritization aid, not a vendor statement. Scoring never changes a classification, and a scored severity never substitutes for missing evidence. Apply [source-policy.md](source-policy.md) to every input used here.

## Dimension 1 — Impact

What happens if the trigger fires, in this project's system context. Choose the worst evidenced consequence, not the typical one.

| Level | Impact | Typical manifestation |
|---|---|---|
| I4 | Data corruption or silent loss of integrity | Corrupted memory, flash, or transferred data; wrong value accepted downstream with no error signalled |
| I3 | Hang, crash, lockup, or unintended reset | Watchdog reset, bus lockup, deadlocked peripheral, unrecoverable state requiring power cycle |
| I2 | Incorrect output or functional error | Wrong but detected result, dropped frame or message, failed transaction, spurious interrupt or flag |
| I1 | Degraded performance or timing margin | Reduced throughput, added latency or jitter, narrowed setup/hold or startup margin, extra retries, higher power |
| I0 | Cosmetic or no functional effect | Documentation defect, harmless status-bit behavior, effect confined to a mode this project never uses |

Cite the vendor-stated consequence for the impact level. Where the project's system context makes the consequence worse than the vendor's description (for example, a dropped message that this design treats as a safety interlock), record the escalation as a separate **Engineering inference**.

## Dimension 2 — Likelihood / exposure

How likely the triggering condition occurs during normal operation of **this** project. Score exposure in the audited configuration, not in the device generally.

| Level | Likelihood | Evidence that supports it |
|---|---|---|
| L4 | Always — on the active path | The trigger condition is unconditionally satisfied by the audited configuration on a path evidenced to run |
| L3 | Frequent — normal operation | The trigger occurs under ordinary workload, common data, or routine mode transitions |
| L2 | Rare / edge-case | The trigger needs an uncommon combination: error recovery, a corner of the operating range, contention, or an infrequent mode |
| L1 | Theoretical-only | The device is in vendor scope, but no evidenced project path reaches the trigger and no plausible one has been identified |

L1 is not Not applicable. Use the classification rules in SKILL.md; L1 records that exposure is unevidenced, not that it is excluded.

## Combination matrix

Read severity mechanically from the two scores.

| Impact \ Likelihood | L4 Always | L3 Frequent | L2 Rare/edge | L1 Theoretical |
|---|---|---|---|---|
| **I4** Data corruption | Critical | Critical | High | Medium |
| **I3** Hang / crash | Critical | High | Medium | Low |
| **I2** Incorrect output | High | High | Medium | Low |
| **I1** Degraded performance/timing | Medium | Medium | Low | Low |
| **I0** Cosmetic | Low | Low | Low | Low |

Record the scoring as `Severity: <level> (Impact I<n> <name>, Likelihood L<n> <name>)` so both inputs stay visible.

Departing from the matrix result is permitted only with an explicit, labeled **Engineering inference** stating the project-specific reason. Never adjust the output silently.

## Unresolved findings

Findings classified **Potentially applicable** or **Requires lab verification** still get a severity so they can be prioritized against resolved ones.

1. Score Impact at the worst plausible consequence consistent with current evidence.
2. Score Likelihood at the highest level the evidence does not rule out.
3. Read the matrix result and label it explicitly: `Provisional (worst-case) severity, pending <specific evidence>`.
4. Name the evidence that would resolve it as **Required follow-up evidence**, and carry it into the report's unknowns section.

Never present a provisional severity as a confirmed one, and never lower it to avoid alarm before the resolving evidence exists.

## Vendor-stated severity

Some vendors classify errata in the errata document itself — a criticality tier, an impact or product-status category, a "no fix planned" disposition. When the document states one:

- Take it as the authoritative starting point and cite it as a **Verified fact** with its exact document location.
- Record it verbatim alongside the derived severity; do not translate it away.
- Score Impact and Likelihood as above. Where they yield a higher severity than the vendor tier, record the difference as a separate labeled **Engineering inference** with its project-specific basis — safety-critical function, real-time deadline, unattended or inaccessible deployment, availability requirement, or any other constraint the vendor could not know. Never overwrite the vendor tier with the escalated value.
- Where they yield a lower severity than the vendor tier, keep the vendor tier. A vendor scope statement outranks a project-side likelihood estimate.

When the document states no severity, record **Unknown** for the vendor tier and derive severity from the two dimensions alone.

## Do not

Do not:

- Infer severity from the erratum title, wording, or tone alone; score the documented consequence and trigger.
- Treat "the vendor did not mark it high" as proof of low real-world impact when this project's use case differs from the vendor's assumed use case.
- Collapse Impact and Likelihood into a single judgment without recording both inputs.
- Fabricate a timing value, electrical limit, failure rate, or vendor severity tier to justify a score. Record it as **Unknown**.
- Lower Likelihood because a triggering path was not found. Absence of a found call site is not evidence of non-exposure.
- Raise or lower severity to match a desired conclusion, schedule, or remediation cost.
- Leave a Severity field blank on any analyzed erratum. Unresolved findings get a provisional severity; a Not applicable finding records `Not applicable` with the excluding evidence.
