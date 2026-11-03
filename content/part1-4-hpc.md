# Running on GPU clusters

:::{questions}
- What changes when I move from a workstation to a Slurm cluster?
- How do I prepare jobs instead of running them directly?
:::

:::{objectives}
- Know which steps depend on the GPU you install on
- Write Slurm job scripts for CatBench and fine-tuning without submitting them
:::

## Install where you will compute

Some frameworks compile kernels for the GPU they are installed on (NequIP with
OpenEquivariance, Allegro with CuEquivariance, and the D3 kernel). Verification
also needs a GPU. On a cluster, run the installation inside a GPU job or an
interactive GPU session, on the same kind of GPU you will use for production.

To prepare NequIP or Allegro jobs for a different GPU architecture, pass it
explicitly, for example:

```python
resolve("NequIP", arch="sm86")
```

## Things to check on a cluster

- **Internet access.** Compute nodes often have none. Weights must be fetched
  where the network works, before the jobs start.
- **Disk.** About 5 to 15 GB per model. Put the hub on a filesystem that compute
  nodes can read, with enough quota.
- **Drivers.** Each environment pins its own CUDA build; check that the cluster's
  NVIDIA driver is new enough for the models you need.
- **Speed tests.** Keep benchmark output on fast local storage. Writing every step
  to a slow network filesystem can make a model look slower than it is.

## Write job scripts, submit yourself

The CatBench and fine-tuning tools can write Slurm scripts without running them:

```bash
python run_examples/catbench_quickstart.py MamunHighT2019 --only MACE \
    --slurm --emit-only --partition <partition>
python scripts/ft_run.py MACE --dataset frames.traj --out ft_mace \
    --slurm --partition <partition>
```

The generated header sets only the job name, partition, log files, one task and
one GPU. Wall time, memory, CPUs and account are left to your cluster's defaults,
so add them before submitting if those defaults do not suit.

:::{discussion}
On your cluster, where would you keep the hub: in your home directory, on a
project filesystem, or on scratch? What are the trade-offs?
:::

:::{keypoints}
- Install and verify on the same kind of GPU you will run on.
- Fetch weights where the network works; compute nodes often have no internet.
- Let the tools write job scripts, then add your cluster's resources and submit.
:::
