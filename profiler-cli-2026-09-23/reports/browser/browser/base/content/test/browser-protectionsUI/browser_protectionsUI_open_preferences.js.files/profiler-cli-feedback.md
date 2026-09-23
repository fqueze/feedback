# profiler-cli feedback

## Question: "over which time range is this thread's data complete?" (buffer overflow)

- Profile: `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LP-t-la0Rkerhckdn6pRzQ/runs/0/artifacts/public/test_info/profile_browser_protectionsUI_open_preferences.js.json`
- `profile info` / `thread info` say the thread spans the full 2m49s, and `thread info` gives
  "220 samples and 6885135 markers". Nothing says that samples and instant markers exist only
  for the last ~3 s, because 6.8 M a11y markers overflowed the buffer. The only survivors from
  earlier are long interval markers that ended late.
- Getting the answer took one `zoom push` + `thread markers --search X` per time slice, eight
  slices in all.
- `thread info` could print the earliest sample and earliest instant-marker time per thread,
  and flag "data before T was dropped (buffer full)", the way the profiler front-end shows a
  hatched region.

## Minor

- `thread samples-top-down --max-depth 4` → `error: unknown option '--max-depth'`. The option
  is `--max-lines`, which caps nodes rather than depth. A depth cap would make a shallow
  overview of a deep native stack cheap.
