# Assumptions of the simulation

Here I list every number the simulation needs that doesn't come from the sequence tables, and I say how much I can back it up. I use four labels. **Cited** means there is a source, with its limits. **Default** is a conventional starting value with no data behind it. **Assumption** means I looked and found no data. **From data** means I estimated it from the sequence tables.

My results are only valid inside the ranges below. Where I vary a value, the table says so.

## Parameters I vary

| Parameter | Meaning | Values | Status |
|---|---|---|---|
| K | µm of metaphase chromosome per Mb | 0.015, 0.03, 0.05 | 0.015 **cited**, with limits (below); 0.03 and 0.05 **assumption**. 0.05 gives about 8 µm for chromosome 1 |
| F | how many times the block the C-band looks (pericentromere included) | 1, 1.5, 2.5 | **assumption**. I found no measurement of goat C-bands. 2.5 is the top of the range |
| Optical resolution | smallest band, or difference between bands, that can be told apart | 0.1, 0.25, 0.4 µm | 0.25 µm is a rough value from the Rayleigh criterion (0.61 λ / NA, with λ = 550 nm and NA = 1.4, which gives about 0.24 µm), not checked against C-banding practice; **assumption** |
| Measurement error of the C-band | multiplicative | 10, 20, 30 % | **default** |
| Metaphases per animal | | 10, 20 | **default**; about 20 is the figure reported in classic C-band heteromorphism work (I still have to confirm the exact reference) |
| Heteromorphism spread | sigma of the true difference between the two copies of a chromosome | 0.40 (reference-animal-like) and 0.17 (Kiko × Saanen-like) | **from data** (the firm pairs of stage 2) |

### About K

A 2025 volume electron microscopy study of human RPE1 cells ([J Cell Biol 223:e202409044](https://rupress.org/jcb/article/223/11/e202409044/277020/How-condensed-are-mitotic-chromosomes-Nucleosome)) reports that metaphase chromosomes pack about 66 Mb per µm, which gives K ≈ 0.015 µm/Mb. I treat this with care. It is a single source, from another species, measured in intact cells (chromosomes spread on a slide are stretched and look longer), and I read it through its abstract and a search summary, not in full. I found no absolute chromosome length in µm for the goat, only relative size classes.

### About F

Goat karyotype papers describe the C-band as centromeric, or at the proximal part of the long arm, in all autosomes, but they don't measure its size. One study describes the X and Y as C-negative. That leaves F unconstrained.

## Where the centromere sits inside the block

The sequence gives me the satellite block, not the functional centromere (CENP-A), which could be anywhere inside a block of 1–10 Mb. In the karyotype table and the idiogram I place the centromere in the **middle of the block**. This is an **assumption**, and it only matters for the centromeric index, the arm ratio, the Levan class and the white dot of the idiogram (`levan_if_block_mid`). The heteromorphism and simulation results use block lengths and don't depend on it. What I did measure is that the autosomal blocks start less than 0.05 Mb from the chromosome end. Whether the chromosomes then count as acrocentric or telocentric depends on this assumption, and the sequence can't settle it.

## Fixed assumptions

These stay the same in every scenario. I list them so they can be challenged, and each one is an assumption with no data behind it unless I say otherwise.

| Parameter | Value | Meaning |
|---|---|---|
| Animals | 6 females | a design choice; my sequence data are from a male and a female F1 |
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
| Calling threshold per metaphase | \|ln(h1/h2)\| ≥ 0.25 | a criterion of my analysis, not of nature |
| Majority rule | 50 % of metaphases must repeat the difference | my calling criterion: a difference that repeats in half of the metaphases |
| True heteromorphism | \|ln(h1/h2)\| ≥ 0.18 (about 20 %) | the definition I use to score sensitivity and specificity |
| Replicates | 300 per scenario | |

## What the ground truth uses and leaves out

The ground truth uses the four haplotypes: the reference animal's P and M (from `centromeres_master.csv`), KikoP and SaanenM. I only keep blocks that are `complete` and not `low_satellite`. That leaves out M-CHI7, M-CHI9 and M-CHI29 (very little satellite, probably missing sequence) and Saanen CHI5 (a gap, with the block cut by the search window). Three more blocks are doubtful: Kiko CHI14 and Saanen CHI7 and CHI15 have large SatI expansions whose origin is uncertain (real, or duplicated by the assembler). I keep them but mark them, and one sensitivity scenario removes them.

The base length of each block is the geometric mean over the usable haplotypes of the four. T2T is the collapsed assembly of the reference animal, so I don't average it in; I use it only as a fallback if no haplotype is usable. The block length is the operational "centromere" of my sequence repository, and it includes 15–55 % of sequence that isn't satellite. The satellite-only version (`sat_bp`) is available as an alternative truth.

For the spread of the true difference between the two copies of a chromosome, I use the firm pairs of stage 2 for each animal, computed as median(|ln ratio|) / 0.6745 / sqrt(2), and I assume it is log-normal. That gives sigma = 0.40 for the reference animal (15 pairs) and 0.17 for the Kiko × Saanen female (19 pairs). Using all usable pairs instead gives 0.39 and 0.17. The reference animal's pairs have a heavy tail (CHI21 at 2.5, CHI12 at 1.4) that a log-normal with this sigma understates. I give each animal its own spread because a single pair may include assembly artefacts.

## Known limits of the setup

Sensitivity has a ceiling by construction. My calling threshold per metaphase (0.25) is above my definition of a true heteromorphism (0.18), so the ceiling is 0.88 for the reference-animal-like spread and 0.67 for the Kiko × Saanen-like one, and I report sensitivity against it. The correlation between band and sequence has a ceiling too (about 0.32 here, see the README), which describes the design and doesn't mean the method fails.

The ground truth, the error and the detection all come from one model. When they agree, that tells me the code is consistent, not that goat cytogenetics behaves this way. And because breed and individual are mixed in the Kiko × Saanen female, I call the differences between her haplotypes "between breeds".
