## Question: "was each failure on a revision before or after the landing of fix X?"

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` per task to get its revision.
- Expected: the revision (ideally the full hg hash, or the git hash) next to each task ID in `--task-ids`, so that "which failures predate bug NNN's landing" is one command.
- Got: task IDs grouped by day only; `fx-tests task` prints a 12-char hg hash, which lando's hg2git API refuses (it wants the full hash). I scraped the full hash from the Treeherder URL in `fx-tests task` output, and looped over 11 tasks in a shell script.
- Workaround: `fx-tests task <id> | grep -o 'revision=[0-9a-f]*'`, then `https://lando.moz.tools/api/hg2git/firefox/<full hash>` and `git merge-base --is-ancestor`.

## Question: "how often does this test fail, counting its runs under its previous path?"

- Command: `fx-tests test browser/base/content/test/browser-referrer/browser_referrer_open_link_in_tab.js` and the same with `.../test/referrer/...`.
- Expected: some hint that the test was moved (both paths have the same file name and component, and the old path stops having runs the day the new one starts), or a way to pass both paths and get one merged table.
- Got: two separate reports; the new-path one does not mention the 8 earlier failures. Totals, per-config rates and history had to be added up by hand.
