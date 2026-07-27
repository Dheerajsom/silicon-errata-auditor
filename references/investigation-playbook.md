# Investigation Playbook

Concrete search and confirmation techniques for locating errata documents, mapping an erratum to project artifacts, and establishing that a peripheral is genuinely used.

Everything here is a **discovery heuristic, not authority**. A naming convention or search pattern locates candidate evidence; only the retrieved vendor document establishes what an erratum requires. Apply [source-policy.md](source-policy.md) to anything you find.

## Locating the errata document

Errata are usually published separately from the reference manual, under a vendor-specific document class. Use these as search leads, then confirm the actual document identity from the document itself.

| Vendor | Typical document class | Search pattern |
|---|---|---|
| STMicroelectronics | Errata sheet, `ES` document number | `<part> errata sheet site:st.com` |
| Texas Instruments | Silicon errata, `SPRZ` document number | `<part> silicon errata site:ti.com` |
| NXP | Chip errata / mask set errata | `<part> chip errata site:nxp.com` |
| Microchip / Atmel | Silicon errata and data sheet clarification | `<part> silicon errata site:microchip.com` |
| Espressif | ECO and workarounds for bugs | `<part> errata site:espressif.com` |
| Renesas | Technical update / usage note | `<part> technical update site:renesas.com` |
| Nordic | Errata document per build code | `<part> errata site:nordicsemi.com` |
| AMD / Xilinx | Answer records, silicon errata | `<part> silicon errata` |
| Infineon / Cypress | Errata sheet | `<part> errata sheet site:infineon.com` |
| Arm (CPU core) | Core-specific errata notice | `Cortex-<core> r<n>p<n> software developers errata notice` |

Additional leads worth checking:

- **Vendor SDK/HAL source.** Vendor libraries frequently name the erratum in a comment or guard macro. Search the SDK tree for `errata`, `erratum`, `workaround`, `ERRATA_`, `WA_`, `silicon revision`, `Rev [A-Z]` — this both locates errata IDs and reveals which workarounds the SDK already applies.
- **Arm CPU errata are separate from the vendor's chip errata.** A device integrating a Cortex-M/A core inherits core errata that the silicon vendor's errata sheet may not restate. Identify the core revision (`r<n>p<n>`) as well as the die revision.
- **Errata scope is per mask set / build code**, not per ordering code. The same part number across mask sets can have different applicable errata.

Record the document identity from the document itself: title, document number, revision, date. Do not infer currency from a search-result date. Mark currency unverified unless you checked an official index or product page.

## Establishing that a peripheral is actually used

The presence of a driver, HAL file, or SDK component proves nothing. Establish use positively through a chain; a break anywhere is evidence of non-use, and an unresolvable link is an unknown, not a negative.

1. **Clock gate enabled** — the peripheral clock enable bit or clock-control call for that instance.
2. **Pin mux / IO assignment** — the peripheral's signals routed to pins, in code or generated configuration.
3. **Initialization executed** — the init function reached from a path that actually runs, not merely defined.
4. **Interrupt path** — vector populated and the interrupt enabled at the controller, if the erratum trigger is interrupt-related.
5. **DMA channel/stream binding**, if the trigger involves DMA.
6. **Build inclusion** — the translation unit is compiled into the image and not dead-stripped.
7. **Runtime confirmation**, where available: boot log, register dump, map file, trace.

Useful searches (ripgrep):

```sh
rg -n 'ENABLE_(CLK|CLOCK)|__HAL_RCC_\w+_CLK_ENABLE|CLOCK_EnableClock|periph_module_enable'
rg -n '\b(I2C|SPI|UART|USART|CAN|USB|ETH|ADC|DAC|TIM|QSPI|SDMMC)[0-9]\b'
rg -n 'IRQHandler|IRQn|NVIC_EnableIRQ|irq_enable|esp_intr_alloc'
rg -n 'DMA[0-9]?_(Stream|Channel)|dma_request|DMA_REQUEST'
```

Confirm inclusion in the built image rather than the source tree — check the link map for the symbol, and the build system for whether the file is in the target's source list.

## Mapping an erratum class to artifacts

Read the erratum's triggering condition first, then search for the conditions it names. This table maps common erratum classes to where the trigger and the workaround live.

| Erratum class | Where the trigger lives | What "not applicable" requires |
|---|---|---|
| Clock / PLL / startup sequencing | Clock tree config, oscillator startup, PLL setup order, `SystemInit`, startup file, generated clock config | Evidence the excluded clock source, frequency band, or transition sequence is never configured |
| Flash / read-while-write / wait states | Flash latency setup, code placement in linker script, dual-bank use, in-application programming, XIP configuration | Evidence the code never executes from the affected bank during the affected operation |
| Cache / coherency / memory ordering | MPU and cache setup, cache maintenance calls, DMA buffer placement, linker section attributes, barriers | Evidence the cache is disabled, or buffers are in a non-cacheable region by linker evidence |
| DMA | Stream/channel setup, transfer sizes, burst and FIFO settings, peripheral-to-memory direction, concurrent stream use | Evidence the affected transfer configuration is never programmed |
| Low power / wake-up | Sleep/stop/standby entry, wake source configuration, regulator mode, wake pin setup, entry/exit sequence code | Evidence the affected low-power mode is never entered |
| I2C / SMBus | Timing register values, clock stretching, analog/digital filter settings, error and NACK handling, bus recovery | Evidence the affected speed mode or filter configuration is never used |
| SPI / QSPI | Mode and clock polarity, chip-select handling, memory-mapped mode, dual/quad settings, DDR mode | Evidence the affected mode is never selected |
| USB / Ethernet | PHY interface and clocking, descriptor handling, endpoint or buffer setup, PHY reset timing | Evidence the interface is unpopulated in hardware or never enabled |
| ADC / analog | Sampling time, channel sequencing, reference and supply configuration, calibration, temperature/voltage sensor use | Evidence the affected channel, sequence, or sampling configuration is never programmed |
| Timers / RTC | Counter and prescaler setup, capture/compare modes, synchronization, tamper and calibration paths | Evidence the affected mode or register-access pattern never occurs |
| Bootloader / security / OTP | Boot pin and option-byte configuration, secure boot, debug lock, key storage, one-time-programmable writes | Evidence the affected boot mode or provisioning step is never used |
| CPU core (Arm) | Core revision, compiler and architecture flags, exception handling, barriers, MPU/TCM use | Core revision evidence excluding the erratum, or evidence the affected instruction sequence cannot be generated |

Do not narrow the search to one artifact type. A trigger often lives in generated configuration or a linker script rather than hand-written C.

## Generated configuration traps

Generated files are a common source of silently removed workarounds. For each workaround found in generated output, identify its source and whether regeneration preserves it.

- **STM32CubeMX** — the `.ioc` file is the source of truth. A workaround hand-edited into generated code outside a `USER CODE BEGIN/END` block is lost on regeneration. Check whether the fix belongs in the `.ioc` instead.
- **Zephyr** — devicetree and overlays gate whether a node is active; `status = "okay"` plus the matching driver Kconfig determines real use. `Kconfig` defaults differ per board.
- **ESP-IDF** — `sdkconfig` is generated; `sdkconfig.defaults` is the tracked source. A workaround depending on a config symbol must be pinned in the tracked file.
- **Vendor SDK/BSP updates** — a workaround inside vendor library source is reverted by an SDK upgrade. Prefer a workaround in project-owned code, or record the SDK version as a constraint.

Flag any workaround whose persistence depends on a regeneration step as a finding, even when the current tree is correct.

## Confirming a workaround survives the build

A source-level workaround can be defeated by the toolchain. Check:

- **Optimization and reordering** — timing- or ordering-critical sequences that rely on `volatile`, barriers, or explicit ordering. Confirm the qualifiers are present rather than assuming compiler cooperation. Where an erratum specifies an instruction sequence, inspect the disassembly.
- **Build variants** — the workaround is compiled into every configuration that ships, not just Debug. Check per-configuration flags and preprocessor guards.
- **Conditional compilation** — a workaround behind a macro requires evidence the macro is defined for the shipping target. Search the build system, not just the header default.
- **Link-time removal** — the symbol survives dead-code elimination and LTO. Check the map file.
- **Revision gating** — a workaround applied conditionally on silicon revision requires evidence the runtime revision check is correct for the audited part.

When any of these cannot be established from available artifacts, downgrade the classification and record the required follow-up evidence.
