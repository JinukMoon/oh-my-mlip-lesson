# Quick reference

All commands run from the hub directory after `source env.sh`.

## Install and verify

```bash
./install.sh MACE                                   # install a framework
./install.sh --status MACE                          # ready / partial / broken / not installed
python scripts/setup_verify.py MACE-MPA-0 --json    # energies and forces on the GPU
OMM_USE_LOCK=1 ./install.sh MACE                    # replay a verified build exactly
```

## Use a model

```python
from oh_my_mlip import resolve, run
spec = resolve("MACE")           # spec["python"], spec["imports"], spec["inference"]
out = run("MACE", atoms)          # one-shot energy and forces from any Python
```

## Benchmark

```bash
python run_examples/catbench_quickstart.py <DATASET> --only MACE,SevenNet [--d3]
python scripts/catbench_report.py --result ./result --out ./report
python3 scripts/catbench_datasets.py --list
```

## Fine-tune

```bash
python scripts/ft_run.py MACE --show-settings
python scripts/ft_run.py MACE --dataset frames.traj --out ft_mace --epochs 50 --seed 0
python scripts/ft_verify.py ft_mace/<checkpoint> --model MACE --json
```

## Distill

```bash
HUB=$PWD
"$PY" scripts/distill_bootstrap.py --teacher MACE-MPA-0 --structure slab.vasp \
    --work ./distill --repo <onthefly-distill> --lmp-bin <lmp>
cd distill && sh run_distill.sh > distill.log 2>&1
"$PY" "$HUB/scripts/distill_verify.py" --work "$PWD" --json   # judged only in production mode
```

## Ask your agent

| Task | Example request |
|---|---|
| Install | Install MACE and check it runs on my GPU. |
| Run | Relax POSCAR with MACE-MPA-0 and give me the relaxed structure. |
| Benchmark | Benchmark MACE and SevenNet on adsorption energies for CO2 reduction on Cu. |
| Your VASP data | Turn my VASP calculations in ./my_dataset into a CatBench dataset. |
| Fine-tune | Fine-tune SevenNet-MF-OMPA on my DFT frames for 100 epochs. |
| Distill | Distill UMA-s-1p2-OMAT into an NN-MTP student for 300 K NVT MD of slab.vasp. |

If something goes wrong: {doc}`troubleshooting`. Full documentation: <https://oh-my-mlip.org>
