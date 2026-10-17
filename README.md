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
3. The earlier simulator reused, with the ground truth rebuilt from the corrected four-haplotype tables.
4. A sensitivity analysis: how detection power changes when the unsupported assumptions change. No single sensitivity or specificity is reported as "the" answer.
5. A public list of assumptions and of what cannot be concluded ([docs/ASSUMPTIONS.md](docs/ASSUMPTIONS.md)).

## What this repository does not do

- It does not predict which C-band a real goat would show. There are no public cytogenetic measurements of this animal.
- It does not measure the functional centromere (CENP-A). The "centromere" is the satellite block of the sequence repository, an operational definition. The functional domain may be much more stable between haplotypes than the satellite array, so a large difference in the array does not imply one in the functional centromere.
- The goat is acrocentric in all autosomes and in the X, so centromeric index and arm ratio carry almost no information. They get one table and one sentence.
- Sensitivity, specificity and power describe the simulated design under the stated assumptions, not the real world.

## The main caution

The optical limit of a light microscope is about 0.25 µm (the author's approximation, to be treated as a parameter). Under some of the assumptions used here, the typical difference between two homologs is near or below that limit. For that reason the optical resolution, the chromosome scale (K, µm per Mb) and the band-to-block factor (F) are varied explicitly, and no goat-specific value exists for K or F ([assumptions](docs/ASSUMPTIONS.md)).

## Inputs

Frozen copies of six small tables from the sequence repository live in `data/input_from_repo1/`, with the commit they come from and their SHA-256 in `SOURCE.md`. The notebook verifies them before reading. Blocks flagged `low_satellite` (M-CHI7, M-CHI9, M-CHI29) are not used as ground truth, and Saanen CHI5 is left out.

## Status

Stages 1 (inference) and 2 (robustness) are done and executed; stage 3 (simulation) is a heading only.

Stage 1, from the frozen tables (`data/outputs/karyotype_inferred.csv`, `expected_heteromorphisms.csv`):
- All 29 autosomes and the X are terminal (acrocentric) in every haplotype (arm ratio above 10). The X is the only chromosome with a real short arm: its satellite block starts 7.2 Mb from the end, and its centromeric index is 0.053.
- Expected heteromorphism, as the median |ln ratio| of the block between the two homologs: 0.37 in IMCG P/M (16 usable pairs) and 0.17 in KikoP/SaanenM (29 usable pairs, labelled "between breeds"). In the rest of the chromosome the same measure is about 0.001-0.002.
- The IMCG pairs are few because 13 of the 29 are incomplete or `low_satellite` in at least one haplotype.

Stage 2 (`data/outputs/robustness_pairs.csv`, `robustness_scenarios.csv`): each usable pair is labelled **firm** or **doubtful** (a doubtful block, or the block length and the satellite-only length disagree by more than 0.2 in |ln ratio|, or the larger haplotype flips).
- IMCG P/M: 15 firm and 1 doubtful. The difference is in the satellite, not in the spacer (Spearman 0.88 between the two measures; the larger haplotype never changes). The median |ln ratio| is 0.37 with the block and 0.37 with the satellite only.
- KikoP/SaanenM: 19 firm and 10 doubtful. The two measures agree much less (Spearman 0.49), and with the satellite only the median difference is larger (0.27 against 0.17), so the non-satellite spacer partly hides differences. Removing the three doubtful blocks barely moves the block-length median (0.16).
- Consequence for stage 3: the ground truth takes the 34 firm pairs; the doubtful ones enter only in a sensitivity scenario. The "size class" of a pair near a class boundary is not stable, so it is not used as a truth label.

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

No downloads and no external tools are needed.

## Licence

Code under MIT ([LICENSE](LICENSE)); text and figures under CC BY 4.0 ([LICENSE-CC-BY-4.0.txt](LICENSE-CC-BY-4.0.txt)).
