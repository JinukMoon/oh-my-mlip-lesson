# Setup

:::{objectives}
- Check that your machine can run oh-my-mlip
- Get the oh-my-mlip hub on your machine, with or without an LLM agent
:::

## What you need

| Requirement | Why |
|---|---|
| Linux x86-64 | The recipes and lock files are built for `linux-64` packages |
| An NVIDIA GPU and driver | A model counts as installed only after it computes energies and forces on the GPU |
| conda or mamba on `PATH` | Every framework gets its own conda environment |
| Disk space | About 5 to 15 GB per installed model (environment plus weights) |
| A Hugging Face account | Only for gated models (UMA, eSEN) |

Each environment pins its own CUDA build, so the minimum NVIDIA driver depends on
which models you install. Check your driver with `nvidia-smi` and compare it with
the [host requirements](https://oh-my-mlip.org/host_requirements/).

## Check before you start

```bash
nvidia-smi            # GPU and driver version (CUDA version at the top right)
conda --version       # or mamba --version
df -h .               # free disk where you will put the hub
```

The first install of a framework downloads several GB of packages and weights,
so it takes from minutes to tens of minutes depending on your network. Compute
nodes on clusters often have no internet access (see {doc}`part1-4-hpc`).

## Words used in this lesson

| Word | Example | Meaning |
|---|---|---|
| framework | `MACE` | an MLIP code; `install.sh` takes a framework or environment name |
| environment | `mace` | the conda environment that holds one framework |
| model | `MACE-MPA-0` | one pretrained checkpoint of a framework; `setup_verify.py` takes a model name |
| GPU-verified | | the model computed energies and forces on the GPU, the only state that counts as installed |

## Get the hub

The hub is a git repository. Environments and local records live inside it.
Weights live in each framework's own cache (for example `~/.cache/mace` or
`~/.cache/huggingface`) or, for some models, under `models/` in the hub.

```bash
git clone https://github.com/JinukMoon/oh-my-mlip.git
cd oh-my-mlip
source env.sh
```

`env.sh` sets the cache and CUDA environment variables the scripts expect. Source
it in every new shell before running anything.

## Optional: connect an LLM agent

With **Claude Code**, add the plugin once:

```text
/plugin marketplace add JinukMoon/oh-my-mlip
/plugin install oh-my-mlip@oh-my-mlip
```

On first use, Claude clones the hub into `~/.oh-my-mlip` (or uses
`$OH_MY_MLIP_HOME` if you set it).

With **Codex or another agent**, tell it:

```text
Clone https://github.com/JinukMoon/oh-my-mlip, read its AGENTS.md and the
documentation at https://oh-my-mlip.org/, and use it for the MLIP work I ask for.
```

`AGENTS.md` is the contract that any agent follows, so the plugin and other agents
run the same steps.

:::{callout} No agent? No problem.
Every exercise in this lesson shows the plain command next to the request you
would give an agent.
:::

:::{callout} No GPU at hand?
You can still follow along: read the plan of an install with
`./install.sh --dry-run <framework>`, list the fine-tuning settings with
`python scripts/ft_run.py <framework> --show-settings`, and write job scripts with
`--emit-only` to run later on a GPU cluster.
:::
