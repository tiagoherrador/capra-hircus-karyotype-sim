# Assumptions of the simulation

Every number the simulation needs and does not get from the sequence tables is listed here, with its status. Statuses: **cited** (a source exists, with its limits), **default** (a conventional starting value, no data behind it), **assumption** (no data found), **from data** (estimated from the sequence tables).

A result of this project is valid only inside the ranges below. Where a value is varied, the table says so.

## Parameters that are varied

| Parameter | Meaning | Values | Status |
|---|---|---|---|
| K | µm of metaphase chromosome per Mb | 0.015, 0.03, 0.05 | 0.015 **cited**, with limits (below); 0.03 and 0.05 **assumption**. 0.05 gives about 8 µm for chromosome 1 |
| F | how many times the block the C-band looks (pericentromere included) | 1, 1.5, 2.5 | **assumption**. No measurement of goat C-bands was found. 2.5 is the top of the range |
| Optical resolution | smallest band, or difference between bands, that can be told apart | 0.1, 0.25, 0.4 µm | 0.25 µm is a rough value from Abbe's formula (0.61 λ / NA, with λ = 550 nm and NA = 1.4, which gives about 0.24 µm), computed by the AI assistant and not checked against C-banding practice; **assumption** |
| Measurement error of the C-band | multiplicative | 10, 20, 30 % | **default** |
| Metaphases per animal | | 10, 20 | **default**; about 20 is the figure reported in classic C-band heteromorphism work (to be confirmed with the exact reference) |
| Heteromorphism spread | sigma of the true difference between homologs | 0.40 (IMCG-like) and 0.17 (Kiko x Saanen-like) | **from data** (firm pairs of stage 2). |

### About K

A 2025 volume electron microscopy study of human RPE1 cells ([J Cell Biol 223:e202409044](https://rupress.org/jcb/article/223/11/e202409044/277020/How-condensed-are-mitotic-chromosomes-Nucleosome)) reports a linear packing of about 66 Mb/µm in metaphase chromosomes, that is K ≈ 0.015 µm/Mb. Limits: one source, another species, intact cells (chromosomes in a slide spread are stretched and look longer), and it was read through its abstract and a search summary, not in full. No absolute chromosome length in µm was found for the goat, only relative size classes.

### About F

Goat karyotype papers describe the C-band as centromeric, or at the proximal part of the long arm, in all autosomes, without measuring its size. The X and Y are described as C-negative in one study. F is therefore not constrained.

## Position of the centromere inside the block

The sequence gives the satellite block, not the functional centromere (CENP-A), which can be anywhere inside a block of 1-10 Mb. The karyotype table and the idiogram place the centromere at the **middle of the block**. This is an **assumption** and it matters only for the centromeric index, the arm ratio, the Levan class and the white mark of the idiogram (`levan_if_block_mid`); the heteromorphism and simulation results use block lengths and do not depend on it. The measured fact is that the autosomal blocks start less than 0.05 Mb from the chromosome end. Whether the chromosomes are then called acrocentric or telocentric depends on this assumption and cannot be settled from the sequence.

## Fixed assumptions

These stay fixed in every scenario and are listed so they can be challenged. Each is an assumption with no data behind it unless said otherwise.

| Parameter | Value | Meaning |
|---|---|---|
| Animals | 6 females | design choice; the sequence data are from a male and a female F1 |
| Condensation per metaphase | σ = 0.10 | common to all chromosomes of a cell |
| Error of chromosome length | σ = 0.05 | multiplicative |
| Absolute error of the C-band | one third of the optical resolution | **assumption**; it is 0.033 µm at a resolution of 0.1 µm |
| Minimum measurable band | the optical resolution | **assumption**; a fixed limit well below the optical one would make almost every band "measurable" and the design look better than it is |
| Variation between animals | σ = 0.10 (centromere), 0.02 (rest) | assumption |
| Composition variation between homologs | σ = 0.08 | assumption |
| FISH gain per probe | SatI 1.0, SatII 1.4, SatIII 3.0 | assumption |
| FISH noise | σ = 0.25 | assumption |
| FISH detection floor | 0.1 ‰ of the metaphase signal | assumption |
| Pair not measurable in a metaphase | 7 % | assumption |
| Calling threshold per metaphase | \|ln(h1/h2)\| ≥ 0.25 | criterion of the analysis, not of nature |
| Majority rule | 50 % of metaphases must repeat the difference | criterion used for calling: a difference that repeats in half of the metaphases |
| True heteromorphism | \|ln(h1/h2)\| ≥ 0.18 (about 20 %) | definition used to score sensitivity and specificity |
| Replicates | 300 per scenario | |

## What the ground truth uses and excludes

- Four haplotypes: IMCG P and M (from `centromeres_master.csv`), KikoP and SaanenM.
- Only blocks that are `complete` and not `low_satellite`.
- Excluded: M-CHI7, M-CHI9, M-CHI29 (very little satellite, probably missing sequence) and Saanen CHI5 (gap, block cut by the search window).
- Doubtful, kept but marked: Kiko CHI14 and Saanen CHI7 and CHI15 (large SatI expansions of uncertain origin: real, or duplicated by the assembler). A sensitivity scenario removes them.
- The base length of each block is the geometric mean over the usable haplotypes of the four (T2T is the collapsed assembly of the IMCG animal, so it is not averaged in; it is the fallback if no haplotype is usable).
- The spread of the true difference between homologs is estimated per animal from the **firm** pairs of stage 2, as median(|ln ratio|) / 0.6745 / sqrt(2): sigma = 0.40 for the IMCG-like scenario (15 pairs) and 0.17 for the Kiko x Saanen-like one (19 pairs), and it is assumed log-normal. The IMCG pairs have a heavy tail (CHI21 at 2.5, CHI12 at 1.4) that a log-normal with this sigma understates. Using all usable pairs instead gives 0.39 and 0.17. Each animal has its own spread because a single pair may include assembly artefacts.
- The block length is the operational "centromere" of the sequence repository. It includes 15–55 % of non-satellite sequence. The satellite-only version (`sat_bp`) is available as an alternative truth.

## Known limits of the setup

- Sensitivity has a ceiling by construction: the calling threshold per metaphase (0.25) is above the definition of a true heteromorphism (0.18). The ceiling is 0.88 (IMCG-like) and 0.67 (Kiko x Saanen-like), and the analysis reports sensitivity against it.
- The correlation between band and sequence has a ceiling by construction (about 0.32 here, see the README). It reports the design, not a failure of the method.
- Ground truth, error and detection all come from one model. Agreement between them says the code is consistent, not that goat cytogenetics behaves like this.
- Breed and individual are mixed in the Kiko × Saanen animal, so differences between its haplotypes are labelled "between breeds".
