## Question: which jobs had failure mode X, and which messages co-occur with it

- Command: `fx-tests test browser/components/backup/tests/browser/browser_settings.js --task-ids --limit 0 --config a11y-checks`, then `--issue 1`.
- Expected: a way to split the 69 failing a11y-checks jobs between the two real failure modes (`Node is not accessible via accessibility API` vs `NotFoundError: Could not get children`), and to see which other messages always come with one of them.
- Got: the Issues block groups by *first* failure per run, and the first message of every run is a `TEST-KNOWN-FAIL` (`handleEvent() was unable to perform a11y checks on hidden node`), so there is a single issue row (109x) covering both modes; `--issue` cannot separate them.
- Workaround: looped `fx-tests task <id> --messages --full-messages` over all 69 task IDs and grepped the per-test message lists. That is what found the discriminator: the `hidden node: id: , tagName: button, className: button` todo is in all 63 jobs of the a11y mode and in none of the 6 others.
- What could have shown it: `--task-ids --message "<substring>"` (tasks whose run logged a message), or an Issues block that skips `TEST-KNOWN-FAIL`/todo messages when picking the "first failure".
