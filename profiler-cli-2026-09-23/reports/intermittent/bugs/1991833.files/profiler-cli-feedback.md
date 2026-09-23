## Question: "at what wall-clock time did this marker happen?" (to align a per-test profile with the job log and other profiles)
- Command: `profiler-cli thread markers --session 1991833-1 --search "name:Process Immediate Launch" --list`
- Expected: an option to print absolute (UTC) times next to `t=`, since the job log is in UTC.
- Got: profile-relative times only; had to add `profile meta --json` `startTime` by hand in a script, for 10 profiles.
