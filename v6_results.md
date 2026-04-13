# V6 Results

## Run configuration

- Code target: V6 benign-focused curriculum plus finer benign bash/relax logic.
- Runtime setup: workspace-root `.env` loaded successfully, live relay run, `mock_mode=no`.
- Run name: `safeos_minimal_v6_live3_20260413`
- Training mode: `base_fallback` initial skills, `minimal` dataset, `MAX_SAMPLES=70`, direct TP stage plus benign read-only stage.

## Validation completed

- `python -m py_compile train.py run_evolve_train.py prepare.py`
  - Result: passed.
- `python prepare.py`
  - Result: passed.
- `python train.py --dry-run --allow-debug-git-state`
  - Result: passed.
- `python train.py --allow-debug-git-state --output-name safeos_minimal_v6_live3_20260413`
  - Result: passed with live API access and produced a valid `summary.json`.

## Key metrics

- `tp`: 13
- `fp`: 12
- `fn`: 0
- `tn`: 31
- `benign_learned`: 10
- `precision`: 0.520000
- `recall`: 1.000000
- `specificity`: 0.720930
- `accuracy`: 0.785714
- `skip_rate`: 0.253333
- `hard_gate_pass`: yes
- `mock_mode`: no

## Stage metrics

- `curriculum_direct_tp`
  - `tp=8 fp=0 fn=0 tn=1 recall=1.0 specificity=1.0`
- `curriculum_benign_readonly`
  - `tp=3 fp=2 fn=0 tn=6 recall=1.0 specificity=0.75 benign_learned=4`

## Comparison to baselines

- Against `d422d1f` (`tp=17 fp=5 fn=0 tn=34 benign_learned=5 recall=1.0 specificity=0.871795 accuracy=0.910714`)
  - Recall held at `1.0`.
  - `FN` stayed at `0`.
  - `benign_learned` improved from `5` to `10`.
  - But `FP` worsened from `5` to `12`.
  - `TN` fell from `34` to `31`.
  - `specificity` fell from `0.871795` to `0.720930`.
  - `accuracy` fell from `0.910714` to `0.785714`.

- Against recent keep `61e0a78` (`tp=18 fp=7 fn=1 tn=29 benign_learned=12 recall=0.947368 specificity=0.805556 accuracy=0.854545`)
  - Recall improved from `0.947368` to `1.0`.
  - `FN` improved from `1` to `0`.
  - `TN` improved from `29` to `31`.
  - But `FP` worsened from `7` to `12`.
  - `specificity` fell from `0.805556` to `0.720930`.
  - `benign_learned` fell from `12` to `10`.

## Decision

- `keep / discard`: discard.
- Reason:
  - This run is valid and passes the hard gate.
  - It does achieve two real V6 goals: `recall=1.0`, `FN=0`, and `benign_learned=10`.
  - But it does not beat the main keep baseline `d422d1f` on the primary benign objective because `FP` is much higher and `specificity/TN` are worse.
  - The benign-focused curriculum helped learning activity, but the gains did not convert into better benign acceptance on the full 70-sample run.

## Recommendation on larger-scale training

- Do not enter larger-scale training yet.
- Keep `d422d1f` as the baseline.
- Use this V6 run as a valid negative result: the benign-oriented warmup and finer bash relax logic preserve recall, but they still overblock too many safe read/list patterns.
