## `fx-tests test` shows nothing of test-verify failures

- Command: `fx-tests test browser/base/content/test/browser-sync/browser_sync.js` (and `--history`, `--task-ids`).
- Expected: the 3 `macosx1500-aarch64/opt-test-verify` failures sheriffs annotated on bug 1966277 (`fx-tests intermittent --bug 1966277` lists them) to appear among the failure modes, or a note that test-verify jobs are not counted.
- Got: only the 32px assertion (2 failures in 1 job). Learned of the TV mode only from `intermittent --bug`.
- Workaround: `fx-tests intermittent --bug <N>` then `fx-tests task <id>` on each.

## Question: "which configs ran this test on this push, and did it pass there" for test-verify

- Command: `fx-tests try 492f0d472bd5 --project autoland --config test-verify --all-jobs --test <path>`.
- Got: `--config cannot be applied to try`. Without `--config`, `--all-jobs` reads every test job of the push (slow) to answer a question about the ~5 TV jobs.
- What would have answered it: allowing `--config` together with `--test` (per-config outcome rows are not changed in meaning by dropping other configs).

## `fx-tests try` on an autoland push without `--project` / hg-edge 406

- Not a fx-tests issue strictly: `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>` (the brief's recipe) returned HTTP 406 from hg-edge.mozilla.org for every attempt (with and without a browser UA/Accept). Workaround: `https://lando.moz.tools/api/hg2git/firefox/<hg rev>` then `git show <git sha>:<path>` in the local checkout.

## `fx-tests try` "Pre-existing" counts the push's own failure as central's

- Command: `fx-tests try 1741efb72eb4 --project autoland --messages`.
- Expected: the 32px assertion flagged as caused by the push (it failed 46 of 47 runs on 23 configs there, and was backed out for it).
- Got: "Pre-existing: central already fails the same way on the 22 configs it failed every run on (2 times in 21 days) — probably not yours." The 2 central failures are the win32 debug job of this very autoland push (eivqMQwbSGes2GktgLMBUw), which sheriffs annotated on the bug. For an autoland push, central data includes the push itself.
- Workaround: checked `git log` for the landing/backout by hand.
