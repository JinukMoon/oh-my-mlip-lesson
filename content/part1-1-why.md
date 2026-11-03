# Why installing MLIPs is hard

:::{questions}
- Why can't I install all MLIP frameworks in one environment?
- What can go wrong without any error message?
- What does oh-my-mlip do about it?
:::

:::{objectives}
- Name the main sources of MLIP installation failures
- Understand the three ideas behind oh-my-mlip: one registry, isolated
  environments, and verification on the GPU
:::

## One environment cannot hold them all

Most MLIP frameworks are built on PyTorch (one, GRACE, on TensorFlow), but each
release pins its own versions of the deep-learning framework, CUDA and helper
libraries. Many also need accelerated kernels compiled for a specific GPU
architecture. Installing two frameworks into one environment usually means one of
them silently gets a version it was never tested with.

Typical problems:

- Conflicting PyTorch or CUDA versions between frameworks
- Kernels compiled for a different GPU architecture than the one you run on
- NVIDIA drivers too old for the CUDA build in the environment
- Weights that must be downloaded from different places, some of them gated
  behind a license agreement

## Silent errors are worse than crashes

A crash at least tells you something is wrong. The dangerous cases finish without
an error:

- The model runs on the CPU instead of the GPU. With an NVIDIA driver older than
  the environment's CUDA build, a few models still run, much more slowly, while
  most refuse to load.
- The wrong checkpoint is loaded, for example a different model size or version.
- A multi-head model computes with a different head than the one you asked for.
  For example, mace-torch only printed a warning for an unknown head name and
  computed with another head. oh-my-mlip stops with an error when the head
  actually used differs from the one requested.

## What oh-my-mlip does

1. **One registry.** `models.json` lists every supported model with its
   environment recipe, where its weights come from, and the exact lines that
   create the ASE calculator.
2. **Isolated environments with recorded builds.** Each framework gets its own
   conda environment, built from a recipe. Every verified build is recorded in a
   lock file, so the same package set can be replayed exactly.
3. **Verification on the GPU.** A model counts as installed only after it has
   computed energies and forces on the GPU. A clean exit of the install script is
   not enough.

The upstream frameworks are run as they are. oh-my-mlip does not reimplement any
model.

:::{discussion}
Have you ever had an MLIP run "successfully" but much slower than expected, or
with results that did not match a colleague's? What turned out to be the cause?
:::

:::{keypoints}
- MLIP frameworks need conflicting software stacks, so each one gets its own
  environment.
- The worst failures are silent: CPU fallback, wrong checkpoint, wrong head.
- oh-my-mlip treats "computes energies and forces on the GPU" as the definition
  of installed.
:::
