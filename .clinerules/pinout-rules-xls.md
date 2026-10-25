# Rule for Cline: Pinout Table (Short)

## Columns
`Pin | Ball | Type | Side | №`

- `Pin` — имя пина (см. Naming).
- `Ball` — координата шара в BGA-корпусе (например, `B4`). **Всегда конкретная координата из вкладки/таблицы BGA-mapping**, даже для повторяющихся имён (`VSS`, `VDD_HV_A` и т.п.) — никогда не дублировать само имя в этой колонке.
- `Type`: `PWR`, `GND`, `BIDIR`, `INPUT`, `OUTPUT`.
- `Side`: `LEFT`, `RIGHT` (см. Grouping).
- `№` — номер пина из Datasheet-таблицы (LQFP и т.д.). Пусто, если у чипа несколько корпусов и пин существует только в BGA (нет LQFP-эквивалента).

## Grouping
1. Split into separate tables by function: `PWR`, `GND`, then one GPIO sheet per port bank (`PTA`, `PTB`, …).
2. GPIO: if the device has port banks, **each bank gets its own sheet named after the bank** (`PTA`, `PTB`, …). If banks are not defined in the datasheet, use a single `GPIO` sheet.
3. GPIO `Side`: a pin with **several mux functions** (its name contains `/`,
   i.e. the pin name plus at least one alternative) gets `Side = LEFT`; a pin
   with a single function gets `Side = RIGHT`.
4. For `Ground` / `Power` blocks with many identical pins — distribute `LEFT` / `RIGHT` roughly evenly.
5. Dedicated control pins (e.g. `RESET_B`, not in the BGA-mapping grid) are placed in the bank sheet that holds the debug/JTAG group, appended at the end of that sheet, `Type = INPUT`.

## Naming
- Multi-function GPIO pins: `<Pin Name>/<FUNC1>/<FUNC2>/...` — no spaces.
  - First item is the **pin name from the Datasheet** (e.g., `PTA4`, `GPIO0`).
  - Then **all alternative functions** from the MUX table in the order given by the Datasheet (`DEFAULT`, `ALT0`, `ALT1`, …).
  - Do **not** wrap in `GPIO[n]`-style brackets (that was specific to the FX3 reference only).
  - Skip duplicates (if a function appears in both DEFAULT and some ALT, list it once).
  - Keep `DISABLED` if it is listed in the Datasheet as a real function value.
- Duplicate pin names: `ORIGINAL_NAME_1`, `ORIGINAL_NAME_2`, … (underscore + number, starting from 1).

## Parsing repeated nets (BGA shared balls)
Many BGA parts have dozens of balls on the same net (`VSS`, `VDD_HV_A`, …) but the main
pinout table lists them as one repeated row (or with a net name instead of a ball
coordinate). Handling:

1. Take the **real ball coordinates from the BGA-mapping sheet** (grid: rows = letters,
   cols = digits) and assign them to the repeated rows in table order.
2. If the main table lists **more rows than the BGA map has balls** for a net
   (datasheet inconsistency), keep only the rows that have a real coordinate —
   **drop the extra rows without a ball** (do not leave an empty `Ball` cell).
3. If the main table lists **fewer rows than balls**, list **every real ball
   of that net** as its own row (do not leave the extra balls out). The
   FC7300F8MDT case: the V1.3 main table lists 14 `VSS` rows, but BGA_mapping
   has 44 `VSS` balls — all 44 must be present, named `VSS_1` … `VSS_44`.
4. After building the sheets, verify: every `Ball` cell matches `[A-Z][1-9][0-9]?`
   and all listed balls are unique.

## Sorting
- GPIO: by port bank ascending, then by pin number (or LQFP order when both apply).
- `PWR` / `GND`: **group pins by base name** (strip the `_N` suffix) — `VDDA_0/VDDA_1`,
  `VDD_HV_A_1…6`, `VSS_1…44` and similar must occupy **neighbouring rows**.
  Group order = first appearance in the datasheet table; inside a group — by suffix number.

## Document Format
- Single `.md` file.
- Title: `# <Device> — Pinout`.
- One `## <Block>` section per functional block.
- Section order: `GND`, `PWR`, then GPIO banks ascending (`PTA`, `PTB`, …; or `GPIO` if no banks).
- Each section contains exactly one table, no extra text between tables.

## Example
```markdown
# FC7300F8MDT — Pinout

## GND
| Pin | Ball | Type | Side | № |
|:----|:-----|:-----|:-----|---:|
| VSSA_0 | K8  | GND | LEFT  |  |
| VSSA_1 | M13 | GND | LEFT  |  |
| VSS_1  | A1  | GND | LEFT  | 177 |
| VSS_2  | A20 | GND | LEFT  | 177 |

## PWR
| Pin | Ball | Type | Side | № |
|:----|:-----|:-----|:-----|---:|
| VDDA_0 | H9 | PWR | LEFT  | 1   |
| VDDA_1 | N11 | PWR | RIGHT | 84  |
| VDD_HV_A_1 | E6 | PWR | LEFT | 19 |
| VDD_HV_A_2 | H12 | PWR | LEFT | 36 |

## PTA
| Pin | Ball | Type | Side | № |
|:----|:-----|:-----|:-----|---:|
| PTA0/DISABLED/FTU4_CH6/FLEXCAN5_RX | B15 | BIDIR | LEFT | 139 |
| PTA4/JTAG_TMS/SWD_DIO/CMP0_OUT | B4 | BIDIR | LEFT | 172 |
| PTA8/ADC1_SE14/FCUART2_RX | A3 | BIDIR | LEFT | 175 |
| RESET_B | B5 | INPUT | RIGHT | 169 |
```

## XLS Output
- Same columns in A–E: `Pin | Ball | Type | Side | №`.
- **Each functional block (`GND`, `PWR`, `PTA`, `PTB`, …) must be placed on a separate worksheet (tab).**
- Worksheet name = block name (e.g., `GND`, `PWR`, `PTA`).
- Sheet order follows the document section order.
- No extra rows, columns, or sheets (no header row inside the sheet).

> ⚠️ **Предупреждение:** Never reorder rows, change pin names/case, or mix blocks in one table. Always split blocks into separate tabs in XLS.