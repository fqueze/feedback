## test_census_filtering_03.js (2026-09-22)

- **Question: which app processes were on the device at time t?** mozdevice logs the whole `ps`
  as a `DEBUG get_process_list: [[1, 'init', 'root'], ...]` marker. `thread markers --list` cuts
  the label after the first kernel threads, so the interesting entries (the app's
  `org.mozilla.geckoview.test_runner:xpcshellN` processes, at the end) never show. Command:
  `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0`.
  Workaround: `--json` and a regex over `data.message`, for 568–971 markers per profile.
  A `--full-messages`-style flag, or a way to grep inside a payload and print only the match
  (e.g. `--extract <regex>`), would have answered it.
- **Question: which slot ($iN) each test ran in, and with which pid.** Needed joining
  `launch_application` (slot, `_TEST_NAME`), `Application ran for` (pid) and `test` markers.
  Command: three `thread markers --search ... --list --limit 0 --json` dumps + a script. Not really
  profiler-cli's job; noting it as the question the default output could not answer.

## review-test_census_filtering_03.js (2026-09-22)

- **profile-link.py refuses a session loaded from a report's own link.** Command:
  `profiler-cli load 'https://profiler.firefox.com/from-url/<encoded taskcluster URL>/marker-table/?marker=N&thread=0&v=17' --session s`
  (as `review-brief.md` says to check links), then `profile-link.py --session s --marker m-3923`.
  Expected: a link. Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
  Workaround: stop, reload the raw Taskcluster URL (another ~0.6 GB load) to link one new marker.
  The from-url link wraps the raw URL, so profile-link.py could unwrap it.
- **Question: how a pid's name changed over time**, and which pids held a given name. Same
  `get_process_list` `--json` dump + a script over every listing, as above.
