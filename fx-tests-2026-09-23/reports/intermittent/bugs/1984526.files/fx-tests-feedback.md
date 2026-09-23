## Question: "does this failure line also show up in green jobs?" (bug 1984526)

- Command: `fx-tests intermittent --bug 1984526`, then `fx-tests task <id> --profiles` on each of the 10 jobs, then `fx-tests errors --message "missing output line for total leaks"`.
- Expected: some way to learn that `TEST-UNEXPECTED-FAIL | leakcheck | utility missing output line for total leaks!` is printed in every debug run of two manifests, including green jobs, and never turns a job orange by itself (mozlog's StatusHandler does not count `mozleak_total` with `bytes: null`).
- Got: `intermittent` lists "Tests named: 10x leakcheck"; `task` lists only the other failing tests, and says nothing about the leak check; `errors` has no match (leakcheck lines are not in the errors file).
- Workaround: Treeherder REST API to list the push's green debug browser-chrome jobs, each one's errorsummary.log to find which chunk ran the manifest, then its live_backing.log. That is 3 scripts and ~20 MB of logs.
- What would have answered it: the resource-usage profiles already carry `MozLeakTotal` markers ("Leaked Total", with process, scope, induced crash and a red/grey color). `fx-tests task` could list the red ones with their scope, and an aggregate over jobs (green ones included) could say "this leakcheck message comes from manifest X in N of N runs".
