# KNOWN_ISSUES.md

## calculator.html — applyLang() id mismatch (46 broken wiring calls, discovered during CALC-002)

**Not fixed in CALC-002 by explicit decision — documented here for the next session.**

While tracing the `appMethod` label for the CALC-002 Bug 3 field, an audit of every `applyLang()`
wiring call in `calculator.html` against the real HTML `id="..."` attributes found that **46 of 65
calls silently fail**: `applyLang()` calls `s('lbl_xxx', ...)` / `h('lbl_xxx', ...)` (underscore),
but the corresponding HTML label almost always uses `id="lbl-xxx"` (hyphen). Since `s()`/`h()` no-op
when `g(id)` finds nothing, these 46 labels **never translate** — they stay stuck showing whatever
English text is hardcoded in the HTML, regardless of the language selected in `langSelect`.

**`index.html` (the rep tool) has no such problem** — audited separately, all 80 of its wiring calls
resolve correctly (it consistently uses hyphenated ids on both sides).

**LANG-001's translation data is not the problem.** Every language block has the correct, complete
key set (verified independently) — this is purely a DOM-wiring mismatch on the `calculator.html` side.

**Suggested fix (mechanical, low-risk):** rename the id argument in each broken `applyLang()` call
from `lbl_xxx` to `lbl-xxx` (or, where noted below, the actual existing id) to match the HTML. This
does not change `applyLang()`'s structure — it stays an explicit per-ID enumeration, no loop
introduced — so it stays inside the "don't refactor `applyLang()`" constraint. After fixing, re-run
the applyLang()-vs-`id=` audit (a small script — see the CALC-002 session — extracts every
`s('id',`/`h('id',` call and cross-checks it against every `id="..."` in the file) and confirm 0 broken.

| # | Call in applyLang() | Likely intended HTML id | Currently stuck showing (EN, hardcoded) | Correct EN value (from LANG.en) |
|---|---|---|---|---|
| 1 | `s('lbl_crop', ...)` | `id="lbl-crop"` | Crop | Crop |
| 2 | `s('lbl_area', ...)` | `id="lbl-area"` | Planted area (Ha) | Planted area (Ha) |
| 3 | `s('lbl_yield', ...)` | `id="lbl-yield"` | Current yield (MT/Ha) | Current yield (MT/Ha) |
| 4 | `s('lbl_price', ...)` | `id="lbl-price"` | Sale price per MT | Sale price per MT |
| 5 | `s('lbl_apps', ...)` | `id="lbl-apps"` | ⚠ Applications per season | Applications per season |
| 6 | `s('lbl_appMethod', ...)` | `id="lbl-appMethod"` | Application method | Application method |
| 7 | `s('lbl_scTitle', ...)` | `id="lbl-scTitle"` | Sugarcane — RV% inputs | Sugarcane — RV% inputs |
| 8 | `s('lbl_rvCur', ...)` | `id="lbl-rvCur"` | Current RV% | Current RV% |
| 9 | `s('lbl_rvProj', ...)` | `id="lbl-rvProj"` | Projected RV% with PV | Projected RV% with PV |
| 10 | `s('lbl_sgPrice', ...)` | `id="lbl-sgPrice"` | Sugar price per MT | Sugar price per MT |
| 11 | `s('lbl_uplift', ...)` | `id="lbl-uplift"` | Expected yield uplift | Expected yield uplift |
| 12 | `s('lbl_fertSection', ...)` | `id="lbl-fertSection"` | Fertiliser saving (optional) | Fertiliser saving (optional) |
| 13 | `s('lbl_fertKg', ...)` | `id="lbl-fertKg"` | Fertiliser used (kg/Ha) | Fertiliser used (kg/Ha) |
| 14 | `s('lbl_fertPrice', ...)` | `id="lbl-fertPrice"` | Fertiliser price (R/kg) | Fertiliser price (R/kg) |
| 15 | `s('lbl_fertCostLabel', ...)` | `id="lbl-fertCostLabel"` | Current fertiliser cost per Ha | Current fertiliser cost per Ha |
| 16 | `s('lbl_fertReduc', ...)` | `id="lbl-fertReduc"` | Expected fertiliser reduction | Expected fertiliser reduction |
| 17 | `s('lbl_fertSavLabel', ...)` | `id="lbl-fertSavLabel"` | Estimated fertiliser saving per Ha | Estimated fertiliser saving per Ha |
| 18 | `s('lbl_projectedReturns', ...)` | `id="lbl-projectedReturns"` | Projected returns | Projected returns |
| 19 | `s('lbl_totalArea', ...)` | `id="lbl-totalArea"` | Total area | Total area |
| 20 | `s('lbl_hectares', ...)` | `id="lbl-hectares"` | hectares | hectares |
| 21 | `s('lbl_yieldUplift', ...)` | `id="lbl-yieldUplift"` | Yield uplift | Yield uplift |
| 22 | `s('lbl_projected', ...)` | `id="lbl-projected"` | projected | projected |
| 23 | `s('lbl_netGain', ...)` | `id="lbl-netGain"` | Net gain (total) | Net gain (total) |
| 24 | `s('lbl_afterCosts', ...)` | `id="lbl-afterCosts"` | after all PV costs | after all PV costs |
| 25 | `s('lbl_roiLabel', ...)` | `id="lbl-roiLabel"` | Return on investment | Return on investment |
| 26 | `s('lbl_seasonComp', ...)` | `id="lbl-seasonComp"` | Season comparison | Season comparison |
| 27 | `s('lbl_withoutPV', ...)` | `id="lbl-withoutPV"` | Without Plantos Verde | Without Plantos Verde |
| 28 | `s('lbl_withPV', ...)` | `id="lbl-withPV"` | With Plantos Verde | With Plantos Verde |
| 29 | `s('lbl_yield1', ...)` | `id="lbl-yield1"` | Yield | Yield |
| 30 | `s('lbl_harvest1', ...)` | `id="lbl-harvest1"` | Total harvest | Total harvest |
| 31 | `s('lbl_revenue1', ...)` | `id="lbl-revenue1"` | Revenue | Revenue |
| 32 | `s('lbl_yield2', ...)` | `id="lbl-yield2"` | Yield | Yield |
| 33 | `s('lbl_harvest2', ...)` | `id="lbl-harvest2"` | Total harvest | Total harvest |
| 34 | `s('lbl_revenue2', ...)` | `id="lbl-revenue2"` | Revenue | Revenue |
| 35 | `s('lbl_revBreakdown', ...)` | `id="lbl-revBreakdown"` | Revenue breakdown | Revenue breakdown |
| 36 | `s('lbl_baseRev', ...)` | `id="lbl-baseRev"` | Base revenue | Base revenue |
| 37 | `s('lbl_revPV', ...)` | `id="lbl-revPV"` | Revenue with PV | Revenue with PV |
| 38 | `s('lbl_pvCost', ...)` | `id="lbl-pvCost"` | PV programme cost | PV programme cost |
| 39 | `s('lbl_netGainBar', ...)` | `id="lbl-netGainBar"` | Net gain | Net gain |
| 40 | `s('lbl_roiDetail', ...)` | `id="lbl-roiDetail"` | Return on investment | Return on investment |
| 41 | `s('lbl_addRev', ...)` | `id="lbl-addRev"` | Additional revenue | Additional revenue |
| 42 | `s('lbl_fertSaving', ...)` | `id="lbl-fertSaving"` | Fertiliser saving | Fertiliser saving |
| 43 | `s('lbl_pvTotal', ...)` | `id="lbl-pvTotal"` | PV programme cost | PV programme cost |
| 44 | `s('lbl_netTotal', ...)` | `id="lbl-netTotal"` | Net gain (total) | Net gain (total) |
| 45 | `s('lbl_netHa', ...)` | `id="lbl-netHa"` | Net gain per Ha | Net gain per Ha |
| 46 | `s('lbl_whyPV', ...)` | `id="lbl-whyPV"` | Why Plantos Verde | Why Plantos Verde |

## How this was found

An audit script extracted every `s('id', ...)`/`h('id', ...)`/`sl('id', ...)`/`sh('id', ...)` call
inside `applyLang()` (regex over the function body) and cross-referenced each literal id string
against every `id="..."` attribute in the file. This check is distinct from — and was missed by —
LANG-001's verification, which only checked direct `g('id')`/`val('id')`/`sv('id')` calls and
never looked through the `s()`/`h()` indirection layer inside `applyLang()` itself.
