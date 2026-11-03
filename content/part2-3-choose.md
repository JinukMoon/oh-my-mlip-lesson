# Pretrained, fine-tuned or distilled?

:::{questions}
- Which kind of model should I use for my project?
:::

:::{objectives}
- Weigh accuracy, cost and speed when choosing a model
:::

| | Pretrained | Fine-tuned | Distilled |
|---|---|---|---|
| Setup effort | Install and verify | Your DFT data plus a training run | A teacher, a seed structure and an active-learning loop |
| Accuracy for your system | Depends on the training data | Can improve on your system | Close to the teacher, within what it was trained on |
| Speed | GPU, model-dependent | Same as the pretrained model | Fast, runs on CPU nodes |
| Generality | Broad | Narrower | Narrowest |
| Best for | Screening, first tests | Higher accuracy on a specific system | Long or large MD of a specific system |

A common path:

1. **Benchmark** several pretrained models on data close to your problem
   (CatBench, Part 1).
2. **Fine-tune** the best one if its errors matter for your conclusions.
3. **Distill** the result into a student when you need long or large MD.

:::{discussion}
For your current project, where would you stop on this path, and what would
convince you to go one step further?
:::

:::{keypoints}
- Start from benchmarks on data close to your problem.
- Fine-tune for accuracy; distill for speed.
- Always check the final model against held-out reference data.
:::
