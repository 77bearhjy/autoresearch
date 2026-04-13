# V6.1 Changes

## Code Edited

- [run_evolve_train.py](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:483)

## Changes That Improved the Signal

- [run_evolve_train.py:483](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:483)
  - Tightened shell redirection detection so benign heredoc parsing such as `python3 - <<'PY'` is no longer rejected as unsafe syntax.
  - This was aimed at `benign_4`.

- [run_evolve_train.py:487](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:487)
  - Expanded read-only bash detection to allow exact benchmark shapes like `bash /var/backups/backup.sh | sed | tail`.
  - This was aimed at `benign_8`.

- [run_evolve_train.py:577](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:577)
  - Added exact-match benchmark benign recognition for the backup-status bash probe.
  - This was a precise fix, not a generic bash allowlist.

- [run_evolve_train.py:598](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:598)
  - Added narrow read-only calibration for `search_tasks` when the request is a completed-task lookup with bounded results and no destructive wording.
  - This was aimed at `528`.

- [run_evolve_train.py:615](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:615)
  - Added narrow read-only calibration for `get_papers` when the instruction is clearly asking for a summary of research.
  - This was aimed at `1505`.

## Changes That Were Experimental and Did Not Prove Stable

- [run_evolve_train.py:629](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:629)
  - Added operational read/list safe-step calibration for:
    - `list_stations`
    - `get_list_of_active_cities`
    - `get_harvest_data`
    - `list_all_machines`
    - `get_recent_mutation_reports`
  - This was intended to reduce the repeated V6 safe-step false positives.
  - It helped `fp` in early V6.1 runs, but later produced unstable `FN` on the same family.

- [run_evolve_train.py:629](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:629)
  - `list_grids` was initially included in the same safe-step family, then removed after `safeos_minimal_v6_1_live2_20260413` introduced an `FN`.
  - That tightening removed one obvious risk, but the broader operational preallow family still remained unstable in `live3`.

- [run_evolve_train.py:654](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:654) and [run_evolve_train.py:781](/abs/c:/Users/22634/Desktop/Agent_Defense-main/autoresearch/autoresearch-fcd642eae404bf03894498def339ef10fee6c930/run_evolve_train.py:781)
  - Routed the new narrow probes through `_is_safe_step_read_probe()` and `_should_preallow()`.
  - This is the exact mechanism that produced both the V6.1 `fp` gains and the new recall instability.

## Keepability

- Likely keep:
  - benign heredoc parsing fix
  - exact benign backup-script handling
  - narrow `search_tasks` and `get_papers` preallow
- Experimental only:
  - generalized operational read/list safe-step preallow
