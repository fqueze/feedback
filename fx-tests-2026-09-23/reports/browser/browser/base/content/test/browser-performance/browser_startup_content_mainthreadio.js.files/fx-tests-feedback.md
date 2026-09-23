# fx-tests feedback

## Question: "where is this failure's profile?" — the test's own profile artifact is not listed

- Command: `fx-tests task U4vp-Z2XQOaMiko3JonAhw --profiles` (also `J84DuC5pQ2WSbDW5-l2tmQ`, `WaQH0Rg_SC-V7JAD1BvO9g`)
- Expected: the failure's profile, since the failure message says `Unexpected main thread I/O behavior during child process startup; open the profile_startup_content_mainthreadio.json artifact in the Firefox Profiler to see what happened`, and the task has `public/test_info/profile_startup_content_mainthreadio.json`.
- Got: `No failing test named a per-test profile in this job.` Only the resource-usage profile is listed.
- Workaround: listed the task's artifacts with `curl .../api/queue/v1/task/<id>/runs/0/artifacts` and loaded the URL by hand.
- What the output could show: any `public/test_info/profile_*.json` artifact of the job, or any `profile_*.json` named in a failure message whatever its wording (the startup-perf tests in `browser/base/content/test/browser-performance/` all write one this way).

## Not fx-tests, but the brief's recipe: hg.mozilla.org raw-file returns 406

- Command: `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/65844140c333/browser/base/content/test/browser-performance/browser_startup_content_mainthreadio.js`
- Got: 302 to `hg-edge.mozilla.org`, then `HTTP/2 406` with an empty body (also with a browser User-Agent and the full hash).
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<full hg hash>` for the git hash, then `git show <git hash>:<path>` in the local checkout (read-only). Short hashes 404 on lando; the full one is in the task's `payload.env.GECKO_HEAD_REV`.
