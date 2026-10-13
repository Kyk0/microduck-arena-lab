# Microduck Arena Lab

**A small robot, three race courses, and a reproducible search for faster gaits.**

![Microduck Arena Lab — actual simulation footage](media/project-cover.png)

We adapted a licensed running policy to the actual Microduck Arena, kept the policy
interface intact, and selected checkpoints by exact five-seed evaluation. The roller
entry reached **official #1 at 1.648 s**; its subsequent **1.516 s** local candidate is
included in this research snapshot. These are MuJoCo simulation results.

[Watch the project film](media/microduck-project-film.mp4) ·
[Detailed workflow](docs/WORKFLOW.md) ·
[Model downloads](https://huggingface.co/Kyry10/microduck-arena-lab) ·
[Website kit](docs/WEBSITE.md)

## Results at the snapshot

| Course | Best local mean | Finishes | Published Arena mean | Selected checkpoint |
|---|---:|---:|---:|---|
| 2 m sprint | **1.084 s** | 5/5 | 1.096 s | D1 launchboost, 900 |
| 1.5 m hill, 8° | **1.360 s** | 5/5 | 1.360 s | H1b adaptation, 500 |
| 2 m roller sprint | **1.516 s** | 5/5 | **1.648 s, official #1** | R2 polish, 1100 |

Local best and published entry can be different policies. The 1.516 s roller candidate
was not yet submitted when this snapshot was assembled. Rankings are a dated snapshot,
not a promise about the live leaderboard. See [model metadata](models/manifest.json)
and [recorded leaderboards](records/) for timestamps, per-seed times and checksums.

The sprint target was 0.988 s and was **not reached** in this snapshot. We include failed
experiments because they explain the useful choices: preserving normalization, matching
the race, measuring launch speed, and keeping nearby checkpoints instead of trusting
the final training iteration.

## Watch the actual policies

| Film | What it shows |
|---|---|
| [Project film](media/microduck-project-film.mp4) | Three courses, real rollouts, and clearly labeled five-seed results |
| [Sprint](media/sprint-launchboost.mp4) | Seed 5, 1.08 s timed finish; local mean 1.084 s |
| [Hill](media/hill-slope-runner.mp4) | Seed 3, 1.34 s timed finish; local mean 1.360 s |
| [Roller](media/roller-glide.mp4) | Seed 3, 1.44 s timed finish; local mean 1.516 s |

Clips include an untimed 0.50 s settle and labeled slow-motion replay. Video duration
includes titles and replay, so it is not the race score. Footage comes from the Arena's
unmodified physics loop; lighting, camera and visual-only meshes are presentation choices.

![Best reliable scores over the experiment sequence](media/experiment-progress.png)

## How we did it

1. **Read the event, then measure baselines.** Check course geometry, rig, start noise,
   actuator delays, command sweep, fall/timeout semantics and untimed settle.
2. **Start from licensed weights.** Hannes' `microduck-running` is Apache-2.0. Unlicensed
   reference policies were benchmarks only, never training parents.
3. **Preserve the actor contract.** Keep 61 observations, 14 actions and the exact
   normalizer. Imported ONNX actors get a fresh critic; our checkpoints resume the
   actor, critic and optimizer together.
4. **Match the race.** Start from STAND, use the real lane and slope/rollers, nominal BAM
   physics, narrow start/delay variation, forward reward, finish bonus and fall penalty.
5. **Choose by the Arena.** Score every 100 iterations, or 50 for polish. Only 5/5
   policies can become an event best. Keep immutable selected artifacts.
6. **Diagnose, then change one thing.** We traced speed, tilt and wall contacts; lower-LR
   roller polish helped, while several static gait trims and later continuations did not.

![Real roller gait preview](media/roller-preview.gif)

## Guides and code

- [Complete workflow and reproduction](docs/WORKFLOW.md)
- [Sprint experiment guide](docs/SPRINT_GUIDE.md)
- [Hill experiment guide](docs/HILL_GUIDE.md)
- [Roller experiment guide](docs/ROLLER_GUIDE.md)
- [Experiment history and lessons](docs/EXPERIMENTS.md)
- [Local workspace map](docs/LOCAL_WORKSPACE.md)
- [Website video integration](docs/WEBSITE.md)
- [Authored experiment overlays](reproduction/) and [recorded results](records/)

This repository packages our experiment additions rather than another full copy of the
upstream simulator. [Install an overlay](docs/WORKFLOW.md#install-an-event-overlay) into a
pinned upstream checkout to run the training scripts. Full weights live on Hugging Face;
they are deliberately excluded from git.

## Credits

Microduck and its simulation stack: [Pollen Robotics](https://github.com/pollen-robotics/microduck_rl).
Licensed warm start: [Hannes von Essen](https://huggingface.co/HannesVonEssen/microduck-running).
Training: [mjlab](https://github.com/mujocolab/mjlab) and
[rsl_rl](https://github.com/leggedrobotics/rsl_rl).
Evaluation: [Microduck Arena](https://huggingface.co/spaces/pollen-robotics/microduck-arena).

Our experiment additions and guides are provided under Apache-2.0. Dependencies and
robot assets retain their original licenses and notices; see [NOTICE](NOTICE).

