# Use a model in your own work

:::{questions}
- How do I use an installed model in my own script?
- How can I compare several models quickly?
:::

:::{objectives}
- Get the calculator lines and the interpreter for a model with `resolve()`
- Relax a structure with an MLIP
- Compare models from one script
:::

Every framework lives in its own environment, so a model always runs under that
environment's Python interpreter.

## Ask your agent

```text
Relax POSCAR with MACE-MPA-0 and give me the relaxed structure.
```

```text
Write an MD script for this slab that uses SevenNet.
```

The agent takes the calculator lines from the registry and runs your script with
the right interpreter.

## In your own script: `resolve()`

Use this for MD, relaxations and anything long. Copy the lines once; your script
does not need oh-my-mlip at run time.

```python
import sys; sys.path.insert(0, "<path to the hub>")
from oh_my_mlip import resolve

spec = resolve("MACE")        # a family, or a version such as "MACE-MH-1-OMAT"
print(spec["python"])         # the interpreter to run your script with
print(spec["imports"])        # import lines
print(spec["inference"])      # the line that creates `calc`
```

Paste `imports` and `inference` into your script as they are, then run it with
`spec["python"]`:

```python
# my_run.py  ->  run with spec["python"]
from ase.io import read
from mace.calculators import mace_mp

atoms = read("POSCAR")
calc = mace_mp(model='medium-mpa-0', dispersion=False, default_dtype='float64', device='cuda')
atoms.calc = calc
print(atoms.get_potential_energy())
```

## Across models from one script

For quick comparisons, each model runs in its own environment process, so the
calling Python needs no model installed:

```python
import oh_my_mlip

out = oh_my_mlip.run("MACE", atoms)          # {"energy": ..., "forces": ...}

with oh_my_mlip.WorkerPool() as pool:        # workers stay alive between calls
    for name in ("MACE", "SevenNet"):
        resp = pool.request(name, atoms)
        print(name, resp["results"]["energy"])
```

Every call crosses a process boundary, so use `resolve()` for MD loops.

## D3 dispersion

```python
out = oh_my_mlip.run("MACE", atoms, apply_d3=True)
```

or, inside the model's environment:

```python
from catbench.dispersion import DispersionCorrection
calc = DispersionCorrection().apply(calc)
```

## Exercises

:::{exercise} Exercise 1: relax a structure
Relax a structure of your choice with `MACE-MPA-0` using the ready-made script.
:::

:::{solution}
```bash
PY=$(python -c 'from oh_my_mlip import resolve; print(resolve("MACE")["python"])')
"$PY" run_examples/relax.py MACE --structure POSCAR    # writes relaxed.extxyz
```
:::

:::{exercise} Exercise 2: compare two models
Install a second framework and compare its energy for the same structure with
MACE using `WorkerPool`. Is the difference what you expected?
:::

:::{keypoints}
- `resolve()` returns the interpreter and the exact calculator lines for a model.
- `run()` and `WorkerPool` are for quick comparisons across environments.
- Long runs should use `resolve()` inside the model's own environment.
:::
