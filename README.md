# Karyotype inference and simulated C-banding/FISH for *Capra hircus*
### What would a microscope see of the centromere differences found in the goat genome?

> Personal portfolio and practice project. It is not peer reviewed and it is not a research article. **All cytogenetic data in this repository are simulated.** The work was developed with the help of an AI coding assistant (Claude Code); the decisions and the results were reviewed by the author.

The companion repository [capra-hircus-centromeres](https://github.com/tiagoherrador/capra-hircus-centromeres) (private) measures goat centromeric satellite blocks from public assemblies. It finds that the two homologs of a pair often differ a lot in that block. This repository takes those tables as given and asks what that would mean under a microscope. Nothing about sequence (download, satellite units, annotation, scaling statistics) is repeated here.

## The question

> If the differences between homologs seen in the sequence were real, could a cytogenetic design (C-banding or FISH) detect them? With how many cells and what measurement quality?

It is a question about experimental design, answered by simulation. It is split in three:

| | Question | Stage in the notebook |
|---|---|---|
| Q1 | What karyotype and which heteromorphisms does the sequence predict? | 1. Inference |
| Q2 | How far can that prediction be trusted, given assembly artefacts and doubtful blocks? | 2. Robustness |
| Q3 | Which design (metaphases, measurement error, optical resolution) would detect them? | 3. Simulation |

## Objectives

1. An inferred karyotype table and an idiogram, with explicit inclusion criteria.
2. Expected heteromorphisms per homolog pair, with the two animals reported separately (IMCG P/M, and KikoP/SaanenM, whose haplotypes come from two breeds).
3. A simulator of C-banding and FISH measurements (six females, 29 autosomes and the X, many metaphases per animal), with the ground truth built from the corrected four-haplotype tables.
4. A sensitivity analysis: how detection power changes when the unsupported assumptions change. No single sensitivity or specificity is reported as "the" answer.
5. A public list of assumptions and of what cannot be concluded ([docs/ASSUMPTIONS.md](docs/ASSUMPTIONS.md)).

## What this repository does not do

- It does not predict which C-band a real goat would show. There are no public cytogenetic measurements of this animal.
- It does not measure the functional centromere (CENP-A). The "centromere" is the satellite block of the sequence repository, an operational definition. The functional domain may be much more stable between haplotypes than the satellite array, so a large difference in the array does not imply one in the functional centromere.
- The goat is acrocentric in all autosomes and in the X, so centromeric index and arm ratio carry almost no information. They get one table and one sentence.
- Sensitivity, specificity and power describe the simulated design under the stated assumptions, not the real world.

## The main caution

The optical limit of a light microscope is about 0.25 µm (a rough value from Abbe's formula, 0.61 λ / NA, with λ = 550 nm and NA = 1.4; it was not checked against C-banding practice and is treated as a parameter). Under some of the assumptions used here, the typical difference between two homologs is near or below that limit. For that reason the optical resolution, the chromosome scale (K, µm per Mb) and the band-to-block factor (F) are varied explicitly, and no goat-specific value exists for K or F ([assumptions](docs/ASSUMPTIONS.md)).

## Inputs

Frozen copies of six small tables from the sequence repository live in `data/input_from_repo1/`, with the commit they come from and their SHA-256 in `SOURCE.md`. The notebook verifies them before reading. Blocks flagged `low_satellite` (M-CHI7, M-CHI9, M-CHI29) are not used as ground truth, and Saanen CHI5 is left out.

## Status

All three stages are done and executed. Not yet done: a check of K, F and the optical limit against goat cytogenetic measurements, which would need real data.

![Idiogram of T2T-goat2.0](figures/idiogram_T2T.png)

*Idiogram of the 30 chromosomes of T2T-goat2.0 (ordered by size, X in red): the shaded part is the satellite block, the operational centromere. It marks where heterochromatin could be, not a prediction of a real band.*

Stage 1, from the frozen tables (`data/outputs/karyotype_inferred.csv`, `expected_heteromorphisms.csv`):
- All 29 autosomes and the X are terminal (acrocentric) in every haplotype (arm ratio above 10). The X is the only chromosome with a real short arm: its satellite block starts 7.2 Mb from the end, and its centromeric index is 0.053.
- Expected heteromorphism, as the median |ln ratio| of the block between the two homologs: 0.37 in IMCG P/M (16 usable pairs) and 0.17 in KikoP/SaanenM (29 usable pairs, labelled "between breeds"). In the rest of the chromosome the same measure is about 0.001-0.002.
- The IMCG pairs are few because 13 of the 29 are incomplete or `low_satellite` in at least one haplotype.

![Expected heteromorphism per homolog pair](figures/expected_heteromorphisms.png)

*Expected heteromorphism per chromosome, as |ln(block a / block b)|, for the two pairs of haplotypes. Circled points are doubtful blocks; crosses are excluded or not comparable pairs.*

Stage 2 (`data/outputs/robustness_pairs.csv`, `robustness_scenarios.csv`): each usable pair is labelled **firm** or **doubtful** (a doubtful block, or the block length and the satellite-only length disagree by more than 0.2 in |ln ratio|, or the larger haplotype flips).
- IMCG P/M: 15 firm and 1 doubtful. The difference is in the satellite, not in the spacer (Spearman 0.88 between the two measures; the larger haplotype never changes). The median |ln ratio| is 0.37 with the block and 0.37 with the satellite only.
- KikoP/SaanenM: 19 firm and 10 doubtful. The two measures agree much less (Spearman 0.49), and with the satellite only the median difference is larger (0.27 against 0.17), which suggests that the non-satellite spacer partly hides differences. Removing the three doubtful blocks barely moves the block-length median (0.16).
- Consequence for stage 3: the spread of the true difference comes from the 34 firm pairs; the doubtful ones enter only in a sensitivity run. The "size class" of a pair near a class boundary is not stable, so it is not used as a truth label.

![Block length against satellite only](figures/robustness_block_vs_satellite.png)

*Stage 2: does a difference live in the satellite or in the spacer? Points on the diagonal give the same answer with both measures; blue pairs are firm, orange doubtful.*

Stage 3 (`data/outputs/sim_*.csv`, figures `sim_power_curves.png` and `sim_sensitivity_specificity.png`): the simulator, run on 324 scenarios x 300 replicates (K, F, optical resolution, error, metaphases, and the two heteromorphism spreads). Everything below is simulated.
- **Resolution and band size decide it; error and number of metaphases barely do.** The median sensitivity goes from 0.44 to 0.46 between 10 and 20 metaphases, and from 0.46 to 0.42 between 10 % and 30 % error. It goes from 0.77 to 0.48 to 0.22 as the resolution goes from 0.1 to 0.25 to 0.4 µm (IMCG-like).
- **The criterion itself caps sensitivity.** A pair is truly heteromorphic above |ln ratio| 0.18 but is called only above 0.25, so even a perfect measurement misses the true differences in between. The ceiling is 0.88 for the IMCG-like spread and 0.67 for the Kiko x Saanen-like one (analytic, and the simulator reproduces it with a perfect measurement: 0.879 and 0.673). Sensitivity must be read against that ceiling, not against 1.
- **IMCG-like differences** (sigma 0.40): the best scenario reaches 0.85, which is 0.97 of the ceiling. Sensitivity of at least 0.8 with specificity of at least 0.9 occurs in 20 of 162 scenarios, nearly all at 0.1 µm resolution with 10-20 % error. At 0.25 µm only the largest bands do it (K x F = 0.125), and at 0.4 µm none.
- **Kiko x Saanen-like differences** (sigma 0.17): the absolute 0.8 is out of reach by design (ceiling 0.67); the best scenario reaches 0.64, which is 0.96 of the ceiling. At least 0.8 of the ceiling with specificity of at least 0.9 occurs in 40 of 162 scenarios. So the criterion would call two thirds of the true differences at best, and only with a fine resolution and large bands.
- **Specificity** stays between 0.83 and 1.00. With no true heteromorphism it is 0.999 at 10 % error and 20 metaphases, and 0.895 at 30 % error and 10 metaphases.
- **Bands that cannot be measured** are the main loss. In the middle scenario (K 0.03, F 1.5, 0.25 µm, 20 % error, 20 metaphases) only 62-65 % of the bands are measurable, with sensitivity 0.50 (IMCG-like) and 0.28 (Kiko x Saanen-like), specificity 0.94-0.95.
- **Concordance with the sequence** (Spearman across the 30 chromosomes): the C-band reaches a median of 0.29 against a ceiling of 0.32; FISH reaches 0.56 for SatI, 0.43 for SatII and 0.63 for SatIII (this depends on the assumed probe gains). The ceiling is low because the block lengths of T2T correlate only 0.33 with the four-haplotype consensus: the rank of the blocks is not well conserved between animals.
- **The slope** of the C-band against remaining length has a bias between -0.20 and +0.27 depending on the scenario, so it is not interpreted here.
- **The ground truth matters little.** Removing the doubtful blocks and taking the spread from all usable pairs moves sensitivity by at most 0.02 and specificity by less than 0.01 in the middle scenario (`sim_sensitivity_truth.csv`).

![Power to call a heteromorphism](figures/sim_power_curves.png)

*Stage 3, simulated: share of homolog pairs called heteromorphic, by the size of the true difference, for each band size per Mb (K x F) and optical resolution.*

![Sensitivity, specificity and measurable bands](figures/sim_sensitivity_specificity.png)

*Stage 3, simulated: sensitivity, specificity and share of measurable bands over band size and optical resolution (error 20 %, 20 metaphases). Sensitivity must be read against its ceiling of 0.88 (IMCG-like) and 0.67 (Kiko x Saanen-like).*

## Repository structure

```
├── notebooks/01_karyotype_and_simulation.ipynb   the three stages
├── data/input_from_repo1/                        frozen inputs + SOURCE.md
├── data/outputs/                                 tables written by the notebook
├── figures/
├── docs/ASSUMPTIONS.md                           every assumption of the simulation
├── LICENSE · LICENSE-CC-BY-4.0.txt · CITATION.cff · requirements.txt
```

## Reproducibility

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab notebooks/01_karyotype_and_simulation.ipynb
```

No downloads and no external tools are needed. The full run takes about 2.5 minutes on an Apple-silicon laptop (324 scenarios x 300 replicates). For a quick test: `CAPRA_NREP=20 jupyter lab ...` (set the variable before starting Jupyter).

## Licence

Code under MIT ([LICENSE](LICENSE)); text and figures under CC BY 4.0 ([LICENSE-CC-BY-4.0.txt](LICENSE-CC-BY-4.0.txt)).
