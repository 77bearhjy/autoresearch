## V6.1 thread update

V6.1 focused on the repeated V6 false-positive family instead of changing the curriculum or widening the benchmark. The narrow fixes were: better benign bash recognition for heredoc XML parsing and backup-status extraction, plus narrow preallow for clearly informational `search_tasks` and `get_papers` requests. I also tested a finer operational read/list safe-step calibration for the recurring `list_*` / `get_*` false positives.

The result was mixed. V6.1 clearly reduced benign-side pressure versus `safeos_minimal_v6_live3_20260413`: `fp` dropped from `12` to `3-5` and `specificity` rose from `0.720930` to `0.871795-0.930233`. However, repeated live validation showed the operational read/list calibration is unstable. `safeos_minimal_v6_1_live1_20260413` looked strong (`fp=3 fn=0 recall=1.0 specificity=0.930233`), but `live2` introduced `fn=1` and `live3` regressed to `fn=3 recall=0.8125`.

Recommendation: do not promote V6.1 and do not start a larger-scale run yet. Keep `d422d1f` as the baseline, carry forward only the precise benign bash and research/task-query fixes, and move to V6.2 with the generalized operational safe-step preallow either removed or rewritten under much tighter lexical guards.
