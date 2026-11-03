# Troubleshooting and cleanup

:::{objectives}
- Find out what state your installation is in
- Fix the most common problems
- Free disk space and report a problem
:::

## Check the state

From the hub directory:

```bash
python3 scripts/setup_survey.py --table              # every env: ready / partial / missing, gated models, disk
./install.sh --status mace                           # one env's install state; changes nothing
python3 scripts/setup_verify.py MACE-MPA-0 --json    # energies and forces on the GPU
```

Or ask your agent:

```text
setup_verify failed for NequIP-OAM-L. Find out why and fix it.
```

## Common problems

| Symptom | Cause | Fix |
|---|---|---|
| An install was interrupted, or an env is `partial` | The build stopped midway | Run `./install.sh <env>` again. If its imports fail, it stops without deleting anything; `./install.sh --rebuild <env>` deletes the env and builds it again |
| `setup_verify` passes with `degraded: true`, `device: cpu` | The NVIDIA driver is older than the env's CUDA build | Upgrade the driver; see the [host requirements](https://oh-my-mlip.org/host_requirements/) |
| HTTP 401 or 403 when downloading UMA or eSEN | License not accepted, or the token belongs to another account | Accept the license on Hugging Face and run `hf auth login` |
| `EOFError: Ran out of input` or "... does not exist" when a model loads | An empty or partial weight file from an interrupted download | Remove the leftovers under `models/<framework>/` and run again |
| NequIP or Allegro fails to load on a different GPU | The compiled model is for another GPU architecture | Compile for this GPU; see [Accelerators](https://oh-my-mlip.org/compile/) |
| pip times out on `pypi.nvidia.com` | That host is unreachable from your network | `python3 scripts/install_nvidia_wheels.py <env>`, then `./install.sh <env>` again |
| D3 is unavailable | No `nvcc` (CUDA toolkit) | Install a CUDA toolkit and run `install.sh` again; the models themselves still run |
| Loading `UMA-m-1p1-*` ends without an error message (exit code 137) | The checkpoint does not fit in host RAM | Use a machine with 32 GB of RAM or more, or a `UMA-s-*` model |

## Free disk space

```bash
conda env remove -p "$OH_MY_MLIP_HOME/envs/<env>"    # an env built by install.sh
python3 scripts/adopt_env.py --remove <env>          # an env you adopted: removes only the registration
```

Weights live outside the environments: in each framework's own cache (for example
`~/.cache/fairchem`, `~/.cache/mace`, `~/.cache/huggingface`) and, for some
models, under `models/` in the hub. Delete those directories to reclaim their
space; they are downloaded or compiled again when needed.

## Report a problem

Open an issue at <https://github.com/JinukMoon/oh-my-mlip/issues> and include:

- the command or request, and the full error output
- `python3 scripts/setup_verify.py <model> --json` for the model involved
- `nvidia-smi` and `./install.sh --status <env>`

Never include a Hugging Face token or other credentials.
