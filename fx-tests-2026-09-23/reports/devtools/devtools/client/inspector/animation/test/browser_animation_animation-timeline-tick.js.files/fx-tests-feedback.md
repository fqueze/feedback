## `fx-tests test` misses a failing job that `fx-tests task` reports

- Command: `fx-tests test devtools/client/inspector/animation/test/browser_animation_animation-timeline-tick.js` (also with `--task-ids --limit 0` and `--coverage`)
- Expected: both jobs that failed this test on 2026-09-17 counted: `Fd-Uol9MRrm2qGE-N3ZTqg` (linux2404 debug devtools-chrome-http3, autoland ad477655dc82) and `OpXAt8c7QbqF8zNqF2tjpg` (windows11-64-25h2-asan opt devtools-chrome-2, autoland 6e1d83cbc8f3, the job the sheriff cited in the backout on bug 2015990).
- Got: 2 failures on 1 config, 1 job. `--coverage` lists `test-windows11-64-25h2-asan/opt-mochitest-devtools-chrome` as 436 runs, 0 fail. But `fx-tests task OpXAt8c7QbqF8zNqF2tjpg` shows this test with `TIMEOUT — 2 failing executions of 2`.
- Workaround: found the second job through the backout comment on the bug, then `fx-tests task`.
- Question it could not answer: "which jobs failed this test". An undercount here changes the Configs and Frequency conclusions (one config versus two platforms).
