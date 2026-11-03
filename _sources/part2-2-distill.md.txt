# Distill a tailored MLIP

:::{questions}
- How can I run long or large MD when a foundation model is too slow?
- How does distillation make a small model that is accurate for my system?
:::

:::{objectives}
- Explain the teacher-student idea and on-the-fly active learning
- Set up a distillation run from an installed model
- Know what the current engine fixes and what it lets you choose
:::

## Teacher and student

Large foundation models are accurate across chemistry, but they need GPUs and are
slow for long or large MD. Distillation trains a **small, fast student** to
reproduce a **large teacher** on the structures that matter for your simulation.

In oh-my-mlip, the student is an **NN-MTP** potential. It has a LAMMPS pair style
(`nnmtp`), so the student runs MD on CPU nodes with no Python or LibTorch at run
time. Any installed model can be the teacher.

The loop is on-the-fly active learning, run by the
[onthefly-distill](https://github.com/JinukMoon/onthefly-distill) engine:

1. The teacher labels a pool of structures (energies and forces).
2. The student is trained on the pool.
3. The student runs MD in LAMMPS.
4. Where the student breaks down, the teacher labels those structures and they
   are added to the pool.
5. Repeat until the student stays stable for the MD length you need.

The result is a tailored MLIP for your own system: small, fast, and accurate
where it matters for your simulations.

## Splitting GPU and CPU work

| Step | Where it runs |
|---|---|
| Teacher labels | GPU (the foundation model) |
| Student training | GPU |
| Student MD and production runs | CPU nodes, in LAMMPS |

This split is useful on HPC: a small GPU allocation builds the student, and the
long production MD runs on CPU partitions.

## Ask your agent

```text
Distill UMA-s-1p2-OMAT into an NN-MTP student for 300 K NVT MD of slab.vasp.
```

The agent asks for the teacher, how long the student must stay stable, whether
this is a quick trial or a production run with accuracy targets, and where LAMMPS
and the engine are.

## Or run it yourself

From the hub directory:

```bash
HUB=$PWD
PY=$(python3 -c "import oh_my_mlip; print(oh_my_mlip.resolve('MACE-MPA-0')['python'])")
git clone https://github.com/JinukMoon/onthefly-distill.git ../onthefly-distill   # once
scripts/build_lammps_nnmtp.sh --repo ../onthefly-distill                            # once
"$PY" scripts/distill_bootstrap.py --teacher MACE-MPA-0 --structure slab.vasp \
    --work ./distill --repo ../onthefly-distill --lmp-bin <lmp>
cd distill && sh run_distill.sh > distill.log 2>&1
```

`<lmp>` is the LAMMPS binary with the NN-MTP pair style. This quick run only
exercises the loop; it says nothing about the student's accuracy.

For a student you will actually use, run in production mode with explicit
targets (values in `< >` are yours to choose), again from the hub directory:

```bash
cd "$HUB"
"$PY" "$HUB/scripts/distill_bootstrap.py" --teacher MACE-MPA-0 --structure slab.vasp \
    --work ./distill_prod --repo ../onthefly-distill --lmp-bin <lmp> \
    --acceptance --mode production \
    --energy-mae-max <meV/atom> --force-mae-max <meV/A> \
    --target-ps <stable MD length, ps> --max-iter <AL rounds> --no-progress-limit <rounds>
```

and check the verdict, which needs both held-out accuracy and a stable LAMMPS run:

```bash
"$PY" "$HUB/scripts/distill_verify.py" --work ./distill_prod --json
```

Held-out frames you supply with `--heldout-file` must lie within the
compositions the pool spans.

## What the current engine fixes

Know these before you plan a run:

- **At most four chemical elements** in the structure.
- **Student MD** uses a Langevin thermostat at 300 K (NVT-like). Another
  temperature or ensemble is not an option of the current engine.
- **Boundaries:** the input must be fully periodic (a slab keeps its vacuum
  inside the cell); student MD runs with reflecting walls along z.
- **Frozen atoms** (`FixAtoms`) are labelled with the teacher's forces but not
  trained on.
- **Several compositions:** if the student must work for different compositions
  (for example, a slab with different numbers of water molecules), seed the pool
  with all of them. One composition alone fixes only its own total energy.

:::{callout} Accuracy is relative to the teacher
A student learns to reproduce its teacher on your system. It is not more accurate
than the teacher, and it is only reliable within the kinds of structures it was
trained on. Always check held-out errors against the teacher.
:::

## Exercises

:::{exercise} Exercise 1: plan a distillation
For a system from your own research, decide: which teacher, which structures to
seed the pool with, how long the student must stay stable, and which held-out
frames you will use to check it.
:::

:::{exercise} Exercise 2: a quick trial
Run a quick trial distillation with an installed teacher on a small copper slab.
How many rounds did the loop need?
:::

:::{solution}
Make the slab with the teacher's interpreter, fixing the two bottom layers:

```bash
"$PY" - <<'EOF'
from ase.build import fcc111
from ase.constraints import FixAtoms
slab = fcc111("Cu", size=(3, 3, 4), vacuum=10.0, periodic=True)
slab.set_constraint(FixAtoms(indices=[a.index for a in slab if a.tag >= 3]))
slab.write("slab.vasp", format="vasp")
EOF
```

Then run the quick commands above and count the rounds in `distill/distill.log`.
:::

## Outlook: reactive systems

Distilling reactive systems, where bonds break and form, is harder: the pool must
contain the reactive events themselves, not only configurations near equilibrium.
This is ongoing work and is discussed in the webinar.

:::{keypoints}
- Distillation turns a large teacher into a small, fast student tailored to your
  system.
- Active learning adds teacher labels exactly where the student fails.
- Teacher work runs on GPUs; the student's production MD runs on CPU nodes.
:::
