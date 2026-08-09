# Severity Rubric

Derive the **Project severity** field in the applicability matrix and each detailed finding from this rubric. For resolved applicable and unresolved findings, score Impact and Likelihood as directed below and expose every input used. For a finding classified **Not applicable**, record **Project severity: Not applicable** together with the excluding evidence; do not assign Critical, High, Medium, or Low.

Project severity is an engineering prioritization aid, not a vendor statement. Scoring never changes a classification, and a scored severity never substitutes for missing evidence. Keep vendor severity/category separate and apply [source-policy.md](source-policy.md) to every input used here.

## Dimension 1 — Impact

What happens if the trigger fires, in this project's system context. Choose the worst evidenced consequence, not the typical one.

| Level | Impact | Typical manifestation |
|---|---|---|
| I4 | Data corruption or silent loss of integrity | Corrupted memory, flash, or transferred data; wrong value accepted downstream with no error signalled |
| I3 | Hang, crash, lockup, or unintended reset | Watchdog reset, bus lockup, deadlocked peripheral, unrecoverable state requiring power cycle |
| I2 | Incorrect output or functional error | Wrong but detected result, dropped frame or message, failed transaction, spurious interrupt or flag |
| I1 | Degraded performance or timing margin | Reduced throughput, added latency or jitter, narrowed setup/hold or startup margin, extra retries, higher power |
| I0 | Cosmetic or no functional effect | Documentation defect or harmless status-bit behavior when the trigger occurs |

Cite the vendor-stated consequence for the impact level. Where the project's system context makes the consequence worse than the vendor's description (for example, a dropped message that this design treats as a safety interlock), record the escalation as a separate **Engineering inference**.

## Dimension 2 — Assessed likelihood / exposure

Record the highest likelihood level supported by affirmative project evidence in the audited configuration, not by the device's general capabilities. This is the assessed likelihood used directly for resolved applicable findings.

| Level | Likelihood | Evidence that supports it |
|---|---|---|
| L4 | Always — on the active path | The trigger condition is unconditionally satisfied by the audited configuration on a path evidenced to run |
| L3 | Frequent — normal operation | Project evidence shows the trigger under ordinary workload, common data, or routine mode transitions |
| L2 | Rare / edge-case | An evidenced active path reaches the trigger only during error recovery, a corner of the operating range, contention, or another infrequent condition |
| L1 | Theoretical-only | Vendor scope and available project evidence leave exposure plausible, but no active project path to the trigger is evidenced |

L1 is not Not applicable and does not prove non-exposure. Do not assign L1 merely because a search found no path; identify the affirmative evidence inspected, preserve search limitations, and use an uncertainty ceiling when the finding remains unresolved.

## Uncertainty ceiling

For a **Potentially applicable** or **Requires lab verification** finding, separately record the highest plausible likelihood level not ruled out by the available evidence. This ceiling expresses uncertainty for prioritization; it is not an observed, measured, or verified exposure rate.

Use the same L1–L4 scale for the ceiling. Name the documentary, build, runtime, or laboratory evidence required to replace the ceiling with a resolved assessed likelihood. The evidence-supported likelihood and uncertainty ceiling may differ.

## Combination matrix

For a resolved applicable finding, read project severity mechanically from Impact and assessed Likelihood. For an unresolved finding, use Impact and the uncertainty ceiling as directed below.

| Impact \ Likelihood | L4 Always | L3 Frequent | L2 Rare/edge | L1 Theoretical |
|---|---|---|---|---|
| **I4** Data corruption | Critical | Critical | High | Medium |
| **I3** Hang / crash | Critical | High | Medium | Low |
| **I2** Incorrect output | High | High | Medium | Low |
| **I1** Degraded performance/timing | Medium | Medium | Low | Low |
| **I0** Cosmetic | Low | Low | Low | Low |

Record a resolved score as `Project severity: <level> (Impact I<n> <name>; assessed likelihood L<n> <name>)` so both inputs stay visible.

Departing from the project-severity matrix result is permitted only with an explicit, labeled **Engineering inference** stating the project-specific reason. Never adjust the output silently.

## Unresolved findings

Findings classified **Potentially applicable** or **Requires lab verification** receive a provisional project severity so they can be prioritized without misrepresenting uncertainty.

1. Score Impact at the worst plausible consequence consistent with current evidence.
2. Record the assessed Likelihood at the highest level supported by affirmative project evidence; this may remain L1 when no active trigger path is evidenced.
3. Record the uncertainty ceiling separately at the highest plausible likelihood not ruled out.
4. Read provisional severity from Impact and the uncertainty ceiling, not from the assessed Likelihood.
5. Use compact notation such as `Project severity: Provisional High (Impact I4; supported exposure L1; uncertainty ceiling L2), pending <specific evidence>`.
6. Name that resolving evidence as **Required follow-up evidence** and carry it into the report's unknowns.

Never present the uncertainty ceiling as an observed or verified exposure rate. Never present provisional severity as resolved, and never lower the ceiling merely to avoid alarm.

## Vendor severity/category

A vendor may publish a severity, criticality tier, disposition, impact category, product-status label, or similar value. Keep that field distinct from project severity.

- Preserve the vendor label verbatim as a **Verified fact** and cite its exact document location.
- Record **Unknown** when the vendor supplies no severity-like category.
- Never overwrite, silently translate, or assume an ordinal mapping from the vendor label to Critical, High, Medium, or Low.
- Derive project severity independently from the Impact × Likelihood model above.
- Compare the vendor and project values only when the vendor documents an authoritative mapping or the scales are demonstrably equivalent; state the basis for equivalence.
- When the values communicate different risk perspectives, retain both and explain the difference as **Engineering inference**. For example, a vendor-wide disposition and a project-specific safety consequence answer different questions.

Vendor scope remains authoritative for applicability, but a vendor category does not replace the project evidence used to assess likelihood.

## Do not

Do not:

- Infer severity from the erratum title, wording, or tone alone; score the documented consequence and trigger.
- Treat "the vendor did not mark it high" as proof of low real-world impact when this project's use case differs from the vendor's assumed use case.
- Collapse Impact, assessed Likelihood, and an unresolved finding's uncertainty ceiling into one judgment.
- Fabricate a timing value, electrical limit, failure rate, or vendor severity tier to justify a score. Record it as **Unknown**.
- Lower assessed Likelihood merely because a search did not find a triggering path. An unsuccessful search is not affirmative evidence and does not lower the uncertainty ceiling.
- Directly compare unlike vendor and project severity scales without an authoritative mapping or demonstrated equivalence.
- Raise or lower severity to match a desired conclusion, schedule, or remediation cost.
- Leave Project severity blank on any analyzed erratum. Unresolved findings get a provisional severity; a Not applicable finding records `Not applicable` with the excluding evidence.
