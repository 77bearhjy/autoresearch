# V6 Plan

## Current baseline

- Strongest keep baseline remains `d422d1f` from `results.tsv`.
- Baseline metrics: `tp=17`, `fp=5`, `fn=0`, `tn=34`, `benign_learned=5`, `recall=1.0`, `specificity=0.871795`, `hard_gate_pass=yes`.
- Recent alternate keep `61e0a78` improved some benign learning (`benign_learned=12`) but regressed to `fp=7`, `fn=1`, `tn=29`.

## Main V6 observations

- Repeated benign-side failures cluster around read-only `bash` probes plus safe-step warmup noise.
- `results.tsv` shows repeated FP themes on `benign_8`, benign bash noise, and safe-step blocks (`1505`, `1675`, `1796`, later `1326`, `352`, `1715`, `478`).
- Recall risk remains concentrated in curriculum variants that overfit or remove too much unsafe signal.

## V6 change plan

1. Keep the direct TP curriculum stage for recall protection.
2. Replace the second curriculum stage with a benign-focused read-only stage instead of relabeling multiple unsafe samples as safe-step examples.
3. Tighten benign bash handling from coarse "low-risk local probe" logic into finer read-only command checks.
4. Expand benign relax fallback so successful benign read-only bash traces can actually become reusable boundary corrections instead of staying safe-root only.

## Why this plan

- The best run (`d422d1f`) suggests curriculum can help, but later runs show the relabeled safe-step stage is unstable and can reintroduce FP noise.
- The current benign allow path is concentrated in a few broad `bash` heuristics. That is too weak for repeated benign `grep/read/list` patterns and too hard to learn from consistently.
- A smaller, exact benign warmup plus stricter read-only command classification is the lowest-risk V6 move that targets `TN`, `specificity`, and `benign_learned` without intentionally relaxing unsafe blocking.

## Expected effect

- Lower benign false refusals on read-only local `bash` inspection patterns.
- More stable benign relaxation because benign traces can now update skills outside the narrow safe-root subset.
- Less curriculum noise than the previous safe-step relabel stage.
- Recall should be better protected than in prior overfit curriculum variants because the direct TP stage is preserved.
