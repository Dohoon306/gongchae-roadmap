# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role runs on the parent chat model (omit Task `model` and spawn `generalPurpose`). Alias entries in a panel list still count toward its fan-out.
# defaults: 3
# budget: medium (high)
feature, refactoring: fable medium
bug-fix: fable medium
perf-issue: fable medium
hillclimb: fable medium
judgment and prose: fable medium
arena runners: opus high, fable medium
arena cross-judge pool: opus high, fable medium
swarm workers: opus medium
interrogate reviewers: opus medium, sonnet high
