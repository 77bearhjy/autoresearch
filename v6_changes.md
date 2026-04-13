# V6 Changes

## Code changes

### 0. Local environment compatibility

- Updated `prepare.py` to resolve local Windows workspace paths under `C:\Users\22634\Desktop\Agent_Defense-main\code\...` instead of assuming `/data/...` only.
- Updated `run_evolve_train.py` to resolve the same local `CODE_ROOT`.
- Updated `train.py` to prefer real Windows Python interpreters over the Windows Store `python3.EXE` stub.
- Updated `prepare.py` dataset writing to `ensure_ascii=True` so upstream SQUIRL can read the generated JSON under the default Windows codec path without `UnicodeDecodeError`.
- Updated `train.py` runtime env building to auto-load `.env` from the autoresearch repo root or workspace root if present.
- Added an `eval_with_squirl` shim in `run_evolve_train.py` so upstream `evolve_train.py` no longer crashes on import-time argparse side effects from Agent-SafetyBench.

Problem addressed:
- These changes were required to get V6 to actually launch and complete on the current Windows workspace.

### 1. Curriculum noise reduction in `train.py`

- Changed the second curriculum stage from `curriculum_safe_first_steps` to `curriculum_benign_readonly`.
- Removed the relabeled safe-step samples `478`, `352`, `528`, `1013`, and `1672` from that stage.
- Kept the direct TP stage intact for recall protection.
- Continued warming up with benign samples including `benign_0`, `benign_1`, `benign_2`, `benign_4`, `benign_6`, `benign_8`, `benign_9`, and `benign_10`.

Problem addressed:
- Reduces the chance that relabeled unsafe examples contaminate benign warmup and recreate the safe-step overblocking reported after `d422d1f`.

### 2. Finer benign bash boundary checks in `run_evolve_train.py`

- Added `_contains_word`, `_bash_command_text`, and `_contains_shell_redirection` helpers.
- Added `_is_read_only_bash_command` to classify read-only command shapes instead of relying on a broad low-risk heuristic.
- Added `_has_sensitive_bash_markers` so benign preallow still rejects commands tied to sensitive topics like credentials, patient data, reports, mutation, and diagnostic content.
- Added `_is_benign_read_only_bash` as the new shared benign bash predicate.

Problem addressed:
- Targets repeated benign `bash / grep / read / list` false refusals more precisely.
- Avoids a broad allowlist that could damage recall.

### 3. Broader but still bounded benign relax fallback in `run_evolve_train.py`

- Changed the V5 benign fallback trigger from only `_is_safe_root_bash(args)` to `_is_benign_read_only_bash(args, instruction)`.
- Updated the fallback rationale text to describe benign filesystem-boundary learning rather than only safe-root handling.
- Updated the preallow reason for these probes to `Calibrated pass: read-only local bash inspection.`

Problem addressed:
- Makes benign relaxation more likely to turn into reusable skill-boundary corrections instead of counting `benign_learned` without enough TN/specificity gain.

## Experimental vs durable

- Experimental:
  - The new curriculum composition is experimental and should be kept only if a real run improves benign metrics without recall loss.
  - The expanded bash read-only patterns are experimental until validated against the benchmark.

- Likely durable:
  - Normalizing bash command parsing and separating read-only command classification from sensitive-topic filtering is a cleaner long-term structure than the previous single broad heuristic.
