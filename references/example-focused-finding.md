# Worked Example: Focused Finding

This example shows the expected depth and evidence discipline for a focused finding. **The device, document, erratum, and project are fictional.** Use it for structure and reasoning only; never cite any value from it as real.

## Scenario

The user asks: "Does erratum 2.4.1 affect our ACME-X100 data logger?" They supply the fictional errata sheet ACME-ES0001 Rev 3 and the firmware repository. No revision evidence is available.

The fictional erratum states: on revisions A and B, an SPI receive using DMA with the RX FIFO threshold set to "quarter" may drop the final data frame when the transfer length is not a multiple of 4 bytes. No error flag is set. Fixed in revision C. Workaround: use the "full" threshold, or pad every transfer to a multiple of 4 bytes.

---

### Focused finding: ACME-ES0001 erratum 2.4.1, SPI RX DMA drops final frame

- **Scope and device/revision identity:** Firmware repository at commit `a1b2c3d`, target `logger_release`. Ordering code ACME-X100-QFN48 from `hw/bom.csv:12` (**Verified fact**). Silicon revision **Unknown**: no ID-register read, debugger output, package marking, or traceability record was supplied. Revisions A, B, and C are all plausible for this ordering code per ACME-ES0001 Rev 3 §1, Table 1. Confidence in identity: low.
- **Erratum and vendor citation:** ACME-ES0001 Rev 3, §2.4.1, p. 9 (fictional). Scope: revisions A and B; fixed in C. Trigger: SPI RX via DMA, RX FIFO threshold "quarter", transfer length not a multiple of 4 bytes. Consequence: final frame silently dropped, no error flag. Workaround: "full" threshold or 4-byte padding. Limitation stated by vendor: "full" threshold increases receive latency. Document currency: **Unverified document currency**; no official index was checked.
- **Vendor severity/category:** Unknown. ACME-ES0001 Rev 3 assigns no severity or category to §2.4.1.
- **Project evidence:** `src/drivers/spi_flash.c:88` sets `SPI_RXFIFO_QUARTER`; `src/drivers/spi_flash.c:112` starts RX via `dma_start(DMA_CH2, ...)`; `build/logger_release.map` contains `spi_flash_read`, so the driver is linked. Normal log reads pad length with `ALIGN_UP(len, 4)` at `src/app/logger.c:52`. The fault-dump reader at `src/app/fault.c:130` reads `sizeof(struct fault_frame)` bytes without padding; that size depends on `CONFIG_FAULT_EXTENDED` and was not determined for the release build.
- **Classification:** Potentially applicable. The trigger configuration is evidenced on an active driver, but the affected identity (revision A or B) is not established, and whether the fault-dump length is a multiple of 4 is unresolved. Both unknowns can be resolved by documentary or build evidence, so lab verification is not required to classify.
- **Project severity:** Provisional High (Impact I4; supported exposure L1; uncertainty ceiling L2), pending silicon revision evidence and the release-build value of `sizeof(struct fault_frame)`.
- **Analysis:** The vendor trigger has three conditions. DMA receive and the "quarter" threshold are **Verified facts** from the cited source lines. The normal read path cannot meet the length condition because of padding at `logger.c:52` (**Verified fact**). The fault-dump path may meet it; if it does, the dropped frame corrupts a stored fault record with no error, which is I4. Reaching the fault-dump path requires a prior fault, so the worst plausible exposure is L2; no unaligned length is yet evidenced on any path, so supported exposure stays L1 (**Engineering inference**). If the revision proves to be C, the finding becomes Not applicable by vendor scope.
- **Existing mitigation:** Partial and unverified. Padding covers the normal read path. Coverage of the fault-dump path is Unknown: the artifacts neither prove nor disprove an aligned length. No revision-gated workaround or "full" threshold setting was found.
- **Recommended action:** Required: establish the silicon revision first. If A or B is plausible in shipped units, pad the fault-dump read to a multiple of 4 bytes, consistent with the vendor's padding workaround. Optional defence: add `_Static_assert(sizeof(struct fault_frame) % 4 == 0, ...)` so a future struct change cannot reintroduce the trigger. Switching to the "full" threshold is the alternative vendor workaround but adds receive latency per the vendor limitation. Suggested code is untested.
- **Verification procedure:** Static: compute `sizeof(struct fault_frame)` for `logger_release` from the map file or a compile-time assertion; pass if divisible by 4 or padded. Build: confirm the assertion is compiled into the release target. Runtime, if revision A or B is confirmed: trigger a fault, read the dump over SPI, and compare against a checksum; pass if 1,000 consecutive dumps match.
- **Unknowns and required follow-up evidence:** (1) Silicon revision of shipped units, from the ID register as documented in the ACME reference manual, debugger output, or traceability records; revision C closes the finding as Not applicable. (2) Release-build `sizeof(struct fault_frame)`; a multiple of 4 together with no other unpadded path would support Mitigated on revisions A and B. (3) Official currency of ACME-ES0001 Rev 3.

---

## Why this is classified correctly

- **Not "Not applicable":** the revision is unknown, and no positive evidence shows the trigger cannot occur on the fault-dump path.
- **Not "Confirmed exposure":** neither the affected revision nor an unaligned length is positively evidenced. Absence of padding on one path proves nothing until its length is known.
- **Not "Mitigated":** padding is verified on only one of two affected paths.
- **Provisional severity reads from the ceiling (L2), not supported exposure (L1)**, and the notation shows both, as [severity-rubric.md](severity-rubric.md) requires.
