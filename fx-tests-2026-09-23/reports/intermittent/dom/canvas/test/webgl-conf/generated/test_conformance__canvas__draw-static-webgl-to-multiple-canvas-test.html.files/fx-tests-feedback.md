# fx-tests feedback

## "Perma-fail" verdict driven by a `-cf` (confirm-failures) job, for a failure confined to one push

- Command: `fx-tests test dom/canvas/test/webgl-conf/generated/test_conformance__canvas__draw-static-webgl-to-multiple-canvas-test.html`
- Expected: a headline saying that all 9 failures come from one day (2026-09-14) and one push, with nothing since, i.e. "stopped failing". The `-cf` job should also be recognised as a confirm-failures rerun: it is scheduled only after a failure, so 5/5 there is true by construction.
- Got: `Verdict: perma-fail. Never passed on 1 configuration: ...-nofis-cf (5/5)`. A reader skimming the output takes this for an active perma-fail. Seeing that it had stopped took a separate `--history` run.
- Workaround: `--history` and `--config android --day <d> --coverage`.
- Suggestion: leave `-cf` jobs out of the perma-fail verdict, or label them. When every failure is on a single day or push with passes after it, say so in the verdict.

## Android geckoview jobs: no per-test profile, and the decisive evidence was in logcat

- Command: `fx-tests task FOv8yrFmRNiyUyW7dYVpiQ --profiles`
- Got: `No failing test named a per-test profile in this job.` plus the resource-usage URL. That is correct, but the resource-usage profile only has the test's log markers.
- The answer (`[GFX1-]: BlitSdToFramebuffer failed for type 6`, 90 times within the test) was in the `public/test_info/logcat-*.log` artifact. Finding it meant listing the task's artifacts by hand.
- Suggestion: when a job has no per-test profile, print its logcat artifact URL too. For extra credit, show the logcat lines between the failing test's `test_start` and `test_end`.
