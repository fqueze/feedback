## Jobs killed at max run time are dropped although their per-test profiles exist

- Command: `fx-tests task cpNwj0AlRG6CgpABnO8W0Q --profiles` (same for PnIbCzhATU-NpdaP2gPTuQ, SgBibflhRaGLUc12ukmkfA, Uqw_FOBiRMSmrT6N4doCAg, SEHrwNL4RVy6tzhKvoBA2w); and `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: the per-test results and profile URLs of these jobs. Only `profile_resource-usage.json` is a partial stream; `public/test_info/profile_test_tooltip.xhtml.json`, `-2` and `-3` were uploaded and load fine in profiler-cli.
- Got: exit 3, "The job never got to write a profile, so there are no per-test results to read". The push view reports the test as "6 failures in 7 runs ... 1 run not read" and never names the 4 opt jobs at all. Those 5 jobs held 13 of the 16 timeouts of this failure mode, so the tool's counts put it at 3 of 6 and made the other failure mode look like the main one.
- Workaround: list the task's artifacts through the Taskcluster queue API, and grep `public/logs/live_backing.log` for `TEST-START`/`TEST-UNEXPECTED-FAIL` of the test.
