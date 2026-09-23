## Question: "the full revision this job ran at"

- Command: `fx-tests task XT0wgTqgRy2p6j1Wm1Y8qQ --profiles`
- Expected: the push's full 40-char revision, needed to map it to git (`https://lando.moz.tools/api/hg2git/firefox/<rev>` returns 404 for a 12-char short hash), because `hg.mozilla.org` raw-file currently answers HTTP 406 to every request.
- Got: `mozilla-central 8c1187b2fe37` (short). The full hash was only inside the Treeherder URL.
- Workaround: `fx-tests task <id> | rg -o "revision=[0-9a-f]+"`, then hg2git, then `git show <sha>:<path>`.
- Could show: the full revision, or the git sha directly, on the header line.
