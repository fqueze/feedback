## Question: the source of the test at the failing revision

- Command: `fx-tests task SRUn7MTcSHqEses1O5HvPA.0 --profiles`
- Expected: a revision I can read the test at.
- Got: `autoland ace91b8723d2`, a Mercurial hash. `https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>` answers HTTP 406 (via hg-edge) for every form of the hash, so the rev could not be used directly.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<full hg hash>` to get the git hash, then `git show <git hash>:<path>` in the local checkout. Printing the git hash next to the hg one would save both steps.

## Question: why a node test server exited

- Command: `fx-tests task <taskId> --messages`
- Expected: the cause of `child process exit closing code: 1`.
- Got: only the xpcshell message. The cause (`node moz-http2 [stderr] Error: listen EADDRINUSE ... :::<port>`) is only in live_backing.log, near the test's TEST-START, and is not in the per-test profile either.
- Workaround: downloaded the 16 MB live_backing.log and grepped it. Surfacing `node moz-http2 [stderr]` lines emitted during a failing test would have answered it.
