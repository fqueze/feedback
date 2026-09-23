## `fx-tests try` lists a perma-fail as a "new intermittent", without its message

Command: `COLUMNS=250 fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`

Expected: `browser_toolbox_content_modal_overlap.js` under PERMA-FAILS for `opt-mochitest-devtools-chrome-a11y-checks-5`, with its failure message, as the PERMA-FAILS rows show theirs.

Got: listed under NEW INTERMITTENTS as `6/7`, and the row shows no message at all. `--json` says `failedTwice: 3, passed: 0, passedOnRetry: 0, notAnalyzed: 1`: every run that was read failed, both times. The 7th "run" is job run `e6fPXv0dTUWlpLrNpuNcMw.0`, which has no `profile_resource-usage.json` at all (`fx-tests task e6fPXv0dTUWlpLrNpuNcMw.0` exits 4) — a job run that produced nothing, not a pass. Counting it in the denominator turns a deterministic failure into an intermittent.

Workaround: `fx-tests task JQZgTPShQvS04PtDY5jiiA --profiles` for the message, `--json` for the outcomes.

Question the default output did not answer: "did this test ever pass on this push?" — a row could say "failed every run that was read; 1 job run had no data".
