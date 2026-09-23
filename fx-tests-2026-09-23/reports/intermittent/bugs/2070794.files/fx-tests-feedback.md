## Question: how often does a perftest job fail on each device pool, and does it pass anywhere

- Bug 2070794 is a perftest signature. Before blaming the network, I had to know whether the same `shopify-applink-startup` job passes on the a55 (LambdaTest) pool, and how often it fails on p6/s24 (Bitbar). I also needed the rates of the local-page variants on the same devices.
- `fx-tests test <path>` takes a test file. There is none for a perftest job type, and fx-tests has no per-job-type rates for perftest jobs. `fx-tests intermittent --bug` counts only starred jobs, so it cannot give a denominator or say that a55 is 0 of 26.
- What would have answered it: `fx-tests test --job <job type name>` (or a substring such as `shopify-applink-startup`), with failed/total per platform over the window.
- Workaround: Treeherder `/api/project/mozilla-central/jobs/?job_type_name=<name>` for 18 job types (`th_rates.py` here), then downloading 181 logs to count per-attempt image errors (`attempt_rates.txt`).
- The failure line and the misleading "artifact expired" message for `task --profiles` on a perftest job are already logged in `2056657.files/fx-tests-feedback.md`. I hit both again here (`fx-tests task Lly8bHwzQSKf2QpeJCnp-g --profiles`, for a job run 5 hours earlier).
