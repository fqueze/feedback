## review-test_census_filtering_04.js: links from a session loaded from a profiler link

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…/marker-table/?marker=34668&thread=0&v=17' --session X`, then `python3 $D/profile-link.py --session X --marker m-937`
- Expected: a link. The review brief says to load the report's link with `profiler-cli load <link>`.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`, and no link.
- Workaround: reloaded the raw Taskcluster URL in a second session and looked the markers up again by search.

## review-test_census_filtering_04.js: which xpcshell slot a test held (Android remote xpcshell)

- Question: which `$iN` process slot a test that never launched had been given, and whether that slot's previous process was still around.
- Command: `thread markers --search "name:test,launch_application" --list --limit 0 --json`, then a script that parsed `$iN` and `_TEST_FILE` out of the `launch_application` messages, matched them to `test` markers, and worked out which slots were taken when.
- Could have shown: a slot column on remote xpcshell `test` markers, since it is in the launch command.

## review-test_census_filtering_04.js: whether a process appears in each `ps` listing

- Question: in which `get_process_list:` DEBUG listings a given process name or pid appears, over a time range.
- Command: `thread markers --search get_process_list --list --json` plus a regex over each 12 kB message.
- Could have shown: a `--search` that reports which listings contain the term rather than just matching them all (every listing matches `get_process_list`).
