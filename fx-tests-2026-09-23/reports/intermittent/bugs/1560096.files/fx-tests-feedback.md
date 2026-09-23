## Question: which jobs carry a non-orange leakcheck line, and which test spawned the process with no leak log
- Commands: `fx-tests intermittent --bug 1560096 --since 30 --full-messages`, `fx-tests task <id>`, `fx-tests errors --message "missing output line for total leaks"`.
- Expected: for a leakcheck "missing output line" failure, the manifest whose shutdown reported it, and the test running when each process without a TOTAL line was spawned (its `XPCOM_MEM_BLOAT_LOG defined ... _pidN.log` line). Also whether green jobs carry the line, since it never turns a job orange (bug 1591678).
- Got: `intermittent` lists the first lines per job only. `task` lists failing tests but not leakcheck. `errors` has no leakcheck lines ("No markers matched").
- Workaround: downloaded 50 live_backing.log files (~440 MB) and wrote a script (attribute.py) that maps each missing pid to the TEST-START before its bloat-log line. That script answered the bug: 34 of 47 jobs come from one test.
