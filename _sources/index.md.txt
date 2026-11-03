# oh-my-mlip: Machine-Learning Interatomic Potentials on HPC

Machine-learning interatomic potentials (MLIPs) can approach the accuracy of
density functional theory (DFT) at a much lower cost, and many pretrained
"foundation" models are now available. Installing and running them side by side
is still hard: each framework needs its own PyTorch or TensorFlow, CUDA build and
accelerated kernels, and a wrong setup can silently run on the CPU or with the
wrong model.

[oh-my-mlip](https://github.com/JinukMoon/oh-my-mlip) is an open-source hub that
brings the major MLIP frameworks into one registry, installs each in its own
conda environment built from a recipe, and counts a model as installed only after it computes
energies and forces on the GPU. It can be driven by LLM coding agents such as
Claude Code and Codex, and every step also runs from the command line.

This lesson accompanies the two-part ENCCS webinar series. Part 1 covers
installing, running and benchmarking MLIPs. Part 2 covers fine-tuning and
distillation. Each webinar takes one hour; the times below are for working
through the material on your own.

:::{prereq}

- A Linux x86-64 machine or cluster with an NVIDIA GPU
- conda or mamba on your `PATH`
- Basic familiarity with Linux, Python and the command line
- For Part 2: basic familiarity with molecular dynamics (MD)

See {doc}`setup` for details.
:::

```{csv-table}
:delim: ;
:widths: auto

10 min ; {doc}`setup`
10 min ; {doc}`part1-1-why`
20 min ; {doc}`part1-2-install`
20 min ; {doc}`part1-3-use`
15 min ; {doc}`part1-4-hpc`
25 min ; {doc}`part1-5-catbench`
25 min ; {doc}`part2-1-finetune`
35 min ; {doc}`part2-2-distill`
10 min ; {doc}`part2-3-choose`
10 min ; {doc}`troubleshooting`
```

```{toctree}
:caption: Before you start
:maxdepth: 1

setup
```

```{toctree}
:caption: Part 1 - Installing, running and benchmarking
:maxdepth: 1

part1-1-why
part1-2-install
part1-3-use
part1-4-hpc
part1-5-catbench
```

```{toctree}
:caption: Part 2 - Fine-tuning and distillation
:maxdepth: 1

part2-1-finetune
part2-2-distill
part2-3-choose
```

```{toctree}
:caption: Reference
:maxdepth: 1

quick-reference
troubleshooting
```

## Learning outcomes

This material is for computational chemists and materials scientists who want to
use MLIPs, for HPC support staff who install MLIP software for users, and for
anyone curious about how LLM agents can handle the setup of scientific software.

By the end of this lesson, learners should be able to:

- Install an MLIP framework in an isolated environment and confirm that the
  model computes energies and forces on the GPU
- Get a ready-to-use ASE calculator for a supported model and run it in their
  own scripts
- Let an LLM agent do the setup, and check its work
- Prepare MLIP jobs for a Slurm GPU cluster
- Benchmark several MLIPs on adsorption energies with CatBench, including their
  own VASP data
- Fine-tune a pretrained model on their own DFT data with the framework's own
  trainer
- Distill a large model into a small, fast student that runs MD in LAMMPS on
  CPU nodes

## Current scope

The oh-my-mlip recipes and lock files are built for Linux x86-64 (`linux-64`
packages) with NVIDIA GPUs. Arm-based nodes (such as GH200) and AMD GPUs have not
been tested and are not supported yet.

## See also

- oh-my-mlip documentation: <https://oh-my-mlip.org>
- oh-my-mlip source code: <https://github.com/JinukMoon/oh-my-mlip>
- CatBench: <https://github.com/JinukMoon/catbench>
- onthefly-distill (distillation engine): <https://github.com/JinukMoon/onthefly-distill>

:::{admonition} Credit
:class: warning

This lesson was written by Jinuk Moon (Seoul National University) for the ENCCS
webinar series, using the ENCCS
[sphinx-lesson template](https://github.com/ENCCS/sphinx-lesson-template).
:::

::::{admonition} License
:class: attention

:::{admonition} CC BY-SA for media and pedagogical material
:class: attention dropdown

Copyright © 2026 Jinuk Moon. This material is released under the Creative
Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0).

**Canonical URL**: <https://creativecommons.org/licenses/by-sa/4.0/>

[See the legal code](https://creativecommons.org/licenses/by-sa/4.0/legalcode.en)
:::

:::{admonition} MIT for source code and code snippets
:class: attention dropdown

MIT License

Copyright (c) 2026, {{ author }}

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
:::

::::
