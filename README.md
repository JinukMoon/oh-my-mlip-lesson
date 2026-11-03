# oh-my-mlip: Machine-Learning Interatomic Potentials on HPC

Lesson material for the two-part ENCCS webinar series on
[oh-my-mlip](https://github.com/JinukMoon/oh-my-mlip):

- **Part 1:** installing, running and benchmarking MLIPs (with or without an LLM agent)
- **Part 2:** fine-tuning and distillation

Built with the ENCCS [sphinx-lesson template](https://github.com/ENCCS/sphinx-lesson-template).

## Build locally

```bash
uv venv -p 3.13 .venv
uv pip install -p .venv/bin/python .
.venv/bin/sphinx-build -b html content _build/html
```

## License

Lesson material: CC BY-SA 4.0 (`LICENSE`). Code snippets: MIT (`LICENSE.code`).
