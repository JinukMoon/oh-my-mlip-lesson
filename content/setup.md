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

## Get the hub

The hub is a git repository. Everything (environments, weights, local records)
lives inside it.

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
