# Frozen inputs from the sequence repository

These six files are copies of tables from my sequence repository,
[capra-hircus-centromeres](https://github.com/tiagoherrador/capra-hircus-centromeres) (private). I copied them at commit
`423ade065276c7dce19f94a849345081dde908eb` (2026-10-16) and I don't recompute anything here. Before reading them, the notebook
checks the SHA-256 of each file against the list at the bottom of this page.

| Copy here | Original path in the sequence repository |
|---|---|
| `centromeres_master.csv` | `data/centromeres_master.csv` |
| `chromosomes.csv` | `data/chromosomes.csv` |
| `results_summary.json` | `data/results_summary.json` |
| `two_animals_haplotypes.csv` | `data/two_animals/haplotypes.csv` |
| `KikoP_centromeres.csv` | `data/screening/per_assembly/KikoP/centromeres.csv` |
| `SaanenM_centromeres.csv` | `data/screening/per_assembly/SaanenM/centromeres.csv` |

When I use these tables I respect their flags: `complete` and `low_satellite` (M-CHI7, M-CHI9 and M-CHI29 are `low_satellite`).
I also leave out Saanen CHI5, which has a gap and whose block is probably cut by the 15-Mb search window.

## SHA-256

| file | sha256 |
|---|---|
| KikoP_centromeres.csv | fe661e8f7feb63c0cc07ea2bc6775952646b92b367bce3f16d0b87c0e7b5f9db |
| SaanenM_centromeres.csv | 8d74eec9b02abc6c43421d51825ed487f52103b6cc38cc749f800c5b4785a43a |
| centromeres_master.csv | 3f34bd069df470fe8cb4319e4315064d2f51933a7080031429db3ed9094e2d02 |
| chromosomes.csv | ca2a00781fa868b46d859a6ed8467bf55b4391844de1816347fb4b300ea5736e |
| two_animals_haplotypes.csv | caaf603433a68ad92e7049b90e5471e193b17596273c59cedef3637245acaa4c |
| results_summary.json | aeb25be2263485071d198189fda430f4f8e45c32061bfdf6adfdeebd11f8a62f |
