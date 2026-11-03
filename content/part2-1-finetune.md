# Fine-tune a pretrained model

:::{questions}
- When is fine-tuning a pretrained MLIP worth it?
- How do I fine-tune different frameworks without learning each trainer?
:::

:::{objectives}
- Prepare a DFT dataset for fine-tuning
- Inspect the settings a framework really has
- Fine-tune a model and check that the result reloads and runs
:::

:::{callout} Starting with Part 2?
You need the hub ({doc}`setup`) and one GPU-verified model ({doc}`part1-2-install`).
:::

Pretrained models are trained on large, general datasets. For a specific system,
such as one catalyst surface or one electrolyte, fine-tuning on your own DFT data
can improve accuracy where it matters for you.

## One workflow, each framework's own trainer

Every framework fine-tunes differently: MACE takes command-line flags, NequIP and
SevenNet read YAML configs, DeePMD reads `input.json`, and each expects its own
dataset layout. oh-my-mlip converts your data to that framework's format, writes
the framework's own config and command, and runs the upstream trainer. Nothing is
reimplemented.

Which models can be fine-tuned is listed in
[Fine-tuning per framework](https://oh-my-mlip.org/finetune/).

## Ask your agent

```text
Fine-tune MACE on frames.traj - just a quick check that fine-tuning works.
```

```text
Fine-tune SevenNet-MF-OMPA on my DFT frames for 100 epochs.
```

The agent first reads which settings the framework really has (DeePMD counts
steps instead of epochs, not every trainer has EMA or early stopping), then asks
for the exact variant, the training length, batch size, learning rate and loss
weights, and the output directory. Every value comes from you, from the
framework's official fine-tuning example, or from its upstream default, and the
run records which one it was.

## Or run it yourself

```bash
python scripts/ft_run.py MACE --show-settings      # the settings this framework has
python scripts/ft_run.py MACE --dataset frames.traj --out ft_mace --epochs 50 --seed 0
python scripts/ft_verify.py ft_mace/<checkpoint> --model MACE --json
```

| Option | Effect |
|---|---|
| `--show-settings` | list native settings, their defaults and official fine-tuning values |
| `--epochs`, `--max-steps`, `--batch-size`, `--lr` | training length, batch size, learning rate |
| `--energy-weight`, `--force-weight`, `--stress-weight` | loss weights |
| `--version MACE-MH-1-OMAT` | start from a specific checkpoint |
| `--emit-only` | write the dataset, config and command without running |
| `--slurm --partition <p>` | also write a Slurm script (nothing is submitted) |

## Good practice

- **Split by what you want to test.** If the question is "does the model work for
  a composition it has not seen?", hold out whole compositions, not random
  frames.
- **Keep the gas references.** If you compute reaction energies, include the gas
  molecules in the training data, because the model's own gas energies enter
  every reaction energy.
- **Compare with the same budget.** When comparing frameworks, give each the same
  GPU time or number of steps, and report the official defaults separately.
- **Check the license.** A model fine-tuned from a non-commercial checkpoint
  inherits its terms.

## Exercises

:::{exercise} Exercise 1: read the settings
Run `--show-settings` for two frameworks. Which settings does one have that the
other does not?
:::

:::{exercise} Exercise 2: a short fine-tuning run
Fine-tune MACE on a small dataset for a few epochs, then verify the checkpoint.
Use your own DFT frames, or make a toy dataset with ASE's EMT potential. Toy data
only exercises the pipeline; it says nothing about accuracy.
:::

:::{solution}
```bash
PY=$(python -c 'from oh_my_mlip import resolve; print(resolve("MACE")["python"])')
"$PY" - <<'EOF'
from ase.build import bulk
from ase.calculators.emt import EMT
from ase.io import write
frames = []
for i in range(40):
    atoms = bulk("Cu", "fcc", a=3.6, cubic=True).repeat(2)
    atoms.rattle(0.05, seed=i)
    atoms.calc = EMT()
    atoms.get_potential_energy(); atoms.get_forces()
    frames.append(atoms)
write("frames.extxyz", frames)
EOF
python scripts/ft_run.py MACE --dataset frames.extxyz --out ft_mace --epochs 5 --seed 0
python scripts/ft_verify.py ft_mace/<checkpoint> --model MACE --json
```

`<checkpoint>` is the checkpoint file in the output directory; `ft_run.json` there
records what was run.
:::

:::{keypoints}
- `ft_run.py` drives each framework's own trainer behind one interface.
- Look at the real settings first; frameworks differ.
- Design the train and test split around the question you want to answer.
:::
