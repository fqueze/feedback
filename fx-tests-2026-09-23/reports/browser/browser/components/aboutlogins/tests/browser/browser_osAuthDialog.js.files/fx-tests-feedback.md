## Question: when was this failing revision pushed, and what is it in the git checkout?

- Command: `fx-tests task THQ9uMYNRlCP22pXvJSESA --profiles` prints `autoland 2c5befbed0bf`.
- Needed: the push time, to bound "stopped failing after landing X", and the git commit, to read
  the code at that revision in a git checkout (hg.mozilla.org `raw-file`/`json-rev` now answer
  HTTP 406).
- Workaround: Treeherder `api/project/autoland/push/?revision=` for the push time and commit
  message, then `git log --all --grep` on that message to find the git SHA.
- What would have answered it: the push timestamp next to the revision in `task`, and in the
  `--task-ids` list (which is grouped by day only), plus the git SHA when known.
