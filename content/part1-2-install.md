# Install and verify a model

:::{questions}
- How do I install an MLIP framework with oh-my-mlip?
- How do I know the installation really works?
:::

:::{objectives}
- Install one framework and verify a model on the GPU
- Read the verification result
- Install a gated model
:::

## Ask your agent

```text
Install MACE and check it runs on my GPU.
```

The agent installs the environment, compiles what your GPU needs, and reports the
model only once it has computed energies and forces there.

## Or run it yourself

From the hub directory, after `source env.sh`:

```bash
./install.sh MACE                      # a framework name or an env name
./install.sh MACE SevenNet ORB         # several at once
./install.sh --dry-run MACE            # print the plan, install nothing
./install.sh --status MACE             # ready / partial / broken / not installed
```

`install.sh` runs the same steps every time: create the environment from its
recipe, install CatBench, prepare the weights, compile what must be compiled for
your GPU, and mark the environment ready. An interrupted install is checked and
then adopted or rebuilt, never duplicated.

## Verify on the GPU

```bash
python scripts/setup_verify.py MACE-MPA-0 --json
```

The result is one JSON verdict. It passes only if the model computed energies and
forces on the GPU. Read the verdict as it is; do not judge it from `nvidia-smi`
yourself.

## Replay a verified build exactly

Every verified build is recorded in `envs/locks/`. To install exactly that package
set, with no dependency resolution:

```bash
OMM_USE_LOCK=1 ./install.sh MACE
```

## Gated models

UMA and eSEN weights are gated. Accept the license on the model's Hugging Face
page with your account, then log in from your own terminal before installing:

```bash
hf auth login
```

An agent should never ask you to paste a token into the chat.

## Exercises

:::{exercise} Exercise 1: install and verify MACE
Install MACE and verify `MACE-MPA-0` on your GPU, either by asking your agent or
by running the commands yourself.
:::

:::{solution}
```bash
source env.sh
./install.sh MACE
python scripts/setup_verify.py MACE-MPA-0 --json
```
The verdict should report a pass, with energies and forces computed on the GPU.
:::

:::{exercise} Exercise 2: what will be installed?
Before installing a second framework, look at its plan without installing
anything. Which framework would you add next, and why?
:::

:::{solution}
```bash
./install.sh --dry-run SevenNet
```
:::

:::{keypoints}
- `install.sh` builds each framework in its own environment from a recipe.
- `setup_verify.py` is the check that counts: energies and forces on the GPU.
- Lock files let you replay a verified build exactly.
:::
