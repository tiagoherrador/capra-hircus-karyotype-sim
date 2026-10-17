# Assumptions of the simulation

Every number the simulation needs and does not get from the sequence tables is listed here, with its status. Statuses: **cited** (a source exists, with its limits), **inherited** (taken from the earlier simulator, no data behind it), **assumption** (no data found), **from data** (estimated from the sequence tables).

A result of this project is valid only inside the ranges below. Where a value is varied, the table says so.

## Parameters that are varied

| Parameter | Meaning | Values | Status |
|---|---|---|---|
| K | µm of metaphase chromosome per Mb | 0.015, 0.03, 0.05 | 0.015 **cited**, with limits (below); 0.03 and 0.05 **assumption**. 0.05 is the earlier simulator's value (chromosome 1 ≈ 8 µm) |
| F | how many times the block the C-band looks (pericentromere included) | 1, 1.5, 2.5 | **assumption**. No measurement of goat C-bands was found. 2.5 is the earlier simulator's value |
| Optical resolution | smallest band, or difference between bands, that can be told apart | 0.1, 0.25, 0.4 µm | 0.25 is the author's approximation from Abbe's limit (about 0.6 λ / NA); **assumption** until checked against C-banding practice |
| Measurement error of the C-band | multiplicative | 10, 20, 30 % | **inherited** |
| Metaphases per animal | | 10, 20 | **inherited**; about 20 is the figure reported in classic C-band heteromorphism work (to be confirmed with the exact reference) |
| Heteromorphism rate | spread of the true difference between homologs | from the sequence tables, and a moderate fixed value (0.12) | **from data** for the first; the second **inherited** |

### About K

A 2025 volume electron microscopy study of human RPE1 cells ([J Cell Biol 223:e202409044](https://rupress.org/jcb/article/223/11/e202409044/277020/How-condensed-are-mitotic-chromosomes-Nucleosome)) reports a linear packing of about 66 Mb/µm in metaphase chromosomes, that is K ≈ 0.015 µm/Mb. Limits: one source, another species, intact cells (chromosomes in a slide spread are stretched and look longer), and it was read through its abstract and a search summary, not in full. No absolute chromosome length in µm was found for the goat, only relative size classes.

### About F

Goat karyotype papers describe the C-band as centromeric, or at the proximal part of the long arm, in all autosomes, without measuring its size. The X and Y are described as C-negative in one study. F is therefore not constrained.

## Fixed assumptions inherited from the earlier simulator

Kept as they were, listed so they can be challenged. Each is an assumption with no data behind it unless said otherwise.

| Parameter | Value | Meaning |
|---|---|---|
| Animals | 6 females | design of the original project; the sequence data are from a male and a female F1 |
| Condensation per metaphase | σ = 0.10 | common to all chromosomes of a cell |
| Error of chromosome length | σ = 0.05 | multiplicative |
| Absolute error of the C-band | 0.03 µm | **to be replaced** by a value tied to the optical resolution |
| Minimum measurable band | 0.05 µm | **to be replaced** by the optical resolution; at 0.05 µm about 93 % of simulated bands were "measurable", which is optimistic |
| Variation between animals | σ = 0.10 (centromere), 0.02 (rest) | assumption |
| Composition variation between homologs | σ = 0.08 | assumption |
| FISH gain per probe | SatI 1.0, SatII 1.4, SatIII 3.0 | assumption |
| FISH noise | σ = 0.25 | assumption |
| FISH detection floor | 0.1 ‰ of the metaphase signal | assumption |
| Pair not measurable in a metaphase | 7 % | assumption |
| Calling threshold per metaphase | \|ln(h1/h2)\| ≥ 0.25 | criterion of the analysis, not of nature |
| Majority rule | 50 % of metaphases must repeat the difference | criterion of the original project document |
| True heteromorphism | \|ln(h1/h2)\| ≥ 0.18 (about 20 %) | definition used to score sensitivity and specificity |
| Replicates | 300 per scenario | |

## What the ground truth uses and excludes

- Four haplotypes: IMCG P and M (from `centromeres_master.csv`), KikoP and SaanenM.
- Only blocks that are `complete` and not `low_satellite`.
- Excluded: M-CHI7, M-CHI9, M-CHI29 (very little satellite, probably missing sequence) and Saanen CHI5 (gap, block cut by the search window).
- Doubtful, kept but marked: Kiko CHI14 and Saanen CHI7 and CHI15 (large SatI expansions of uncertain origin: real, or duplicated by the assembler). A sensitivity scenario removes them.
- The spread of the true difference between homologs is estimated with a robust scale (MAD) from both animals together, not from one pair. The P–M pair alone was used before, and it may include assembly artefacts.
- The block length is the operational "centromere" of the sequence repository. It includes 15–55 % of non-satellite sequence. The satellite-only version (`sat_bp`) is available as an alternative truth.

## Known limits of the setup

- The correlation between band and sequence has a ceiling by construction (the earlier run gave 0.49). It reports the design, not a failure of the method.
- Ground truth, error and detection all come from one model. Agreement between them says the code is consistent, not that goat cytogenetics behaves like this.
- Breed and individual are mixed in the Kiko × Saanen animal, so differences between its haplotypes are labelled "between breeds".
