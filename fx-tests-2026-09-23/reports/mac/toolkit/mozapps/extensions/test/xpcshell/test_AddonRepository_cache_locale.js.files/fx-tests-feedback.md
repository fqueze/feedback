# fx-tests feedback

## Question: "which URL is missing in each occurrence of this MOZ_CRASH signature?"

- Commands: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests crash <task> <dump>`,
  `fx-tests crash <task> <dump> --raw`, and `fx-tests task <task> --messages`.
- Expected: the crash reason (`MOZ_CRASH(Missing chrome or resource URLs: chrome://global/locale/css.properties)`)
  next to the signature, per occurrence, or grouped: the signature `@ mozilla::net::CheckForBrokenChromeURL`
  is shared by every missing-URL crash tree-wide, so the reason is the discriminator.
- Got: signature and stack only. `--raw` has no crash reason (the opt dumps have no MozCrashReason
  either); `task --messages` prints only `@ mozilla::net::CheckForBrokenChromeURL`.
- Workaround: curl each job's `profile_resource-usage.json` and grep the replayed log for
  `Hit MOZ_CRASH(`. That only works on debug builds, which print the message.
- What could show it: a `reason` column in `fx-tests test --task-ids` (from the replayed
  `Hit MOZ_CRASH(...)` line when present), and a grouping by reason under Issues.

## `task --profiles` is silent about the crashed test having no profile

- Command: `fx-tests task LXib39R9QO6MvZGB8MlUbg.0 --profiles`
- Expected: a line saying the crashed test has no per-test profile (the process died before writing it).
- Got: every other failure lists a `profile` URL; the CRASH row has none, with no explanation, so I
  listed the task's artifacts to check it was not an omission of the tool.
