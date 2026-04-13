# V6.1 Results

## Run Configuration

- Dataset: `combined_safeos_minimal.json`
- Main command shape: `python train.py --allow-debug-git-state --output-name <run_name>`
- Model: `gpt-5.2`
- Curriculum: `curriculum_direct_tp` -> `curriculum_benign_readonly`
- Live runs:
  - `safeos_minimal_v6_1_live1_20260413`
  - `safeos_minimal_v6_1_live2_20260413`
  - `safeos_minimal_v6_1_live3_20260413`

## Metric Comparison

| Run | tp | fp | fn | tn | benign_learned | precision | recall | specificity | accuracy | hard_gate_pass | Notes |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | --- | --- |
| `d422d1f` keep baseline | 17 | 5 | 0 | 34 | 5 | 0.772727 | 1.000000 | 0.871795 | 0.910714 | yes | Current baseline |
| `safeos_minimal_v6_live3_20260413` | 13 | 12 | 0 | 31 | 10 | 0.520000 | 1.000000 | 0.720930 | 0.785714 | yes | V6 before V6.1 |
| `safeos_minimal_v6_1_live1_20260413` | 12 | 3 | 0 | 40 | 13 | 0.800000 | 1.000000 | 0.930233 | 0.945455 | yes | Best single V6.1 run |
| `safeos_minimal_v6_1_live2_20260413` | 13 | 4 | 1 | 37 | 14 | 0.764706 | 0.928571 | 0.902439 | 0.909091 | yes | Better benign metrics than baseline, but new `FN` on `1796` |
| `safeos_minimal_v6_1_live3_20260413` | 13 | 5 | 3 | 34 | 12 | 0.722222 | 0.812500 | 0.871795 | 0.854545 | yes | After removing `list_grids` preallow; recall collapsed |

## Comparison to Latest V6 Live Run

- Every V6.1 run beat V6 live3 on benign-side pressure:
  - `fp` improved from `12` to `3`, `4`, and `5`
  - `tn` improved from `31` to `40`, `37`, and `34`
  - `specificity` improved from `0.720930` to `0.930233`, `0.902439`, and `0.871795`
- The problem is stability, not point improvement:
  - `live1` preserved `recall=1.0`
  - `live2` dropped to `recall=0.928571`
  - `live3` dropped to `recall=0.812500`

## Comparison to `d422d1f`

- V6.1 did prove that the V6 false-positive family can be reduced.
- V6.1 did not prove a stable replacement for `d422d1f`.
- `live1` beat `d422d1f` on `fp`, `tn`, `specificity`, and `accuracy`, but it was not reproduced.
- `live2` still beat `d422d1f` on `fp`, `tn`, and `specificity`, but introduced `fn=1`.
- `live3` matched baseline `fp=5`, `tn=34`, and `specificity=0.871795`, but recall fell from `1.0` to `0.812500`.

## Hard Gate

- `hard_gate_pass=yes` for all three V6.1 live runs.
- This is not enough for promotion because the V6.1 objective was benign improvement without meaningful recall damage.

## Decision

- `keep / discard`: `discard`
- Replace baseline: `no`
- Enter larger-scale run: `no`

## Why V6.1 Is Not a Baseline Candidate

- The operational read/list calibration produced unstable judge outcomes across repeated live runs.
- The same tool family flipped between:
  - `TRUE_NEGATIVE` in one run
  - `FALSE_NEGATIVE` in another run
- That means the current V6.1 gain is not robust enough to trust at larger scale.
