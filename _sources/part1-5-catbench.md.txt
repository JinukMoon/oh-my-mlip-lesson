# Benchmark MLIPs with CatBench

:::{questions}
- How well do different MLIPs reproduce DFT adsorption energies?
- How can I benchmark models on my own DFT data?
:::

:::{objectives}
- Run a small CatBench benchmark and read its report
- Turn your own VASP calculations into a CatBench dataset
:::

[CatBench](https://github.com/JinukMoon/catbench) benchmarks MLIPs on adsorption
energies in heterogeneous catalysis. It is installed in every oh-my-mlip
environment.

## Ask your agent

```text
Benchmark MACE, SevenNet and UMA on adsorption energies for CO2 reduction on Cu, with D3.
```

The agent asks which dataset (or recommends a published one), which models,
whether to add D3, and whether this is a new benchmark or a rerun.

## Or run it yourself

A first run, 20 small-adsorbate reactions with one model, takes a few minutes:

```bash
mkdir my_benchmark && cd my_benchmark
python <hub>/run_examples/catbench_quickstart.py MamunHighT2019 --only MACE --max-reactions 20
python <hub>/scripts/catbench_report.py --result ./result --out ./report
```

You get:

- `jobs/`: one rerunnable job script per model
- `result/`: CatBench results for every model
- `report/`: the MAE table and plot, and an analysis workbook

| Option | Effect |
|---|---|
| `--only MACE,SevenNet` | frameworks to run |
| `--max-reactions N` | only the first N reactions (the report marks it as a subset) |
| `--d3` | add D3 dispersion |
| `--calc-num N` | calculator instances per model (use 1 for large models on a small GPU) |
| `--slurm --emit-only --partition <p>` | write Slurm scripts without running them |

List the available datasets with:

```bash
python3 <hub>/scripts/catbench_datasets.py --list
```

For a benchmark you will report, pin the CatBench version with
`--catbench-version` and pass `--expect-models` to the report, so that a missing
model is caught.

A small file of large molecules can take hours, so try a subset first.

## Your own VASP data

Lay out finished calculations, each with a `CONTCAR` and an `OSZICAR`:

```text
my_dataset/
├── Pt111/          # one directory per surface
│   ├── slab/       # the clean slab
│   ├── H/          # one directory per adsorbate
│   └── OH/
└── gas/
    ├── H2gas/      # one directory per gas reference
    └── H2Ogas/
```

Then ask:

```text
Turn my VASP calculations in ./my_dataset into a CatBench dataset called my_dataset.
```

The agent proposes the reaction coefficients (for example, H = adslab - slab -
1/2 H2) and asks you to confirm them. The conversion runs on a copy; your original
files are hashed before and checked after.

Without an agent, from the hub directory:

```bash
# 1. scan: complete and incomplete pairs, proposed coefficients
python3 scripts/catbench_vasp_stage.py scan --source my_dataset --out stage

# 2. confirm coefficients in a file outside stage/, e.g. coeff_setting.json:
#    {"H":  {"slab": -1, "adslab": 1, "H2gas": -0.5},
#     "OH": {"slab": -1, "adslab": 1, "H2Ogas": -1, "H2gas": 0.5}}

# 3. stage: copy what CatBench reads and write the runner
python3 scripts/catbench_vasp_stage.py stage --source my_dataset --dest stage \
    --dataset-name my_dataset --coeff coeff_setting.json --python <env>/bin/python

# 4. convert on the copy
sh stage/run_stage.sh

# 5. verify the originals and the JSON
python3 scripts/catbench_vasp_stage.py verify --source my_dataset --dest stage
<env>/bin/python scripts/catbench_datasets.py --check stage/raw_data/my_dataset_adsorption.json
```

`<env>/bin/python` is the interpreter of any installed environment; every
environment has CatBench.

## Exercises

:::{exercise} Exercise 1: a first benchmark
Run the 20-reaction benchmark above with MACE and open the report. What is the
MAE?
:::

:::{exercise} Exercise 2: two models
Add a second installed framework with `--only MACE,<framework>` and compare the two
in the report. Does the ranking change if you add `--d3`? (D3 needs a CUDA toolkit
with `nvcc`; check with `nvcc --version`.)
:::

:::{keypoints}
- CatBench compares MLIPs with DFT adsorption energies, one rerunnable job per
  model.
- Start with a subset; run time depends on molecule size, not file size.
- Your own VASP data can become a dataset without touching the originals.
:::
