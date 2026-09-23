# profiler-cli feedback (browser_rules_inherited-element-backed-pseudo-elements.js)

## Marker times rounded to whole seconds in text output

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0 --session …` and `profiler-cli marker info m-16 m-964 m-4260 … --session …`
- Expected: millisecond timestamps (e.g. `t=72.024s`), so I can order a pref write in the parent against a pref read and a DocumentLoad in a content process, and cite times in the report.
- Got: `t=1m12s` / `Time: 1m12s (instant)` for every marker after the first minute. The whole failing test (about 1000 markers) reads `t=1m13s`, so ordering across threads is impossible from the text.
- Workaround: `marker info … --json` and read `.start`. The question was "when exactly did this marker happen, to the ms"; the text output could simply keep 3 decimals past 60 s.

## Garbled JS call tree in a CI content process

- Command: `profiler-cli zoom push 39,40; profiler-cli thread samples-top-down --includes-function f-12430` (t-132, Web Content, task efH6c-fdS6SQRxKbEXwHYw)
- Expected: a plausible JS stack for `page-style.js!#pseudoIsRelevant`, e.g. `getApplied → #getAllElementRules → #pseudoIsRelevant`.
- Got: `getLayout → processMatchedSelectors → #pseudoIsRelevant → self-hosted!split → get keyframesRules → #cacheSheets → Pool.js!manage → #cacheSheet → Actor.js!handler → 0xfabedc1f5f4`, which cannot be a real call chain. So sampled JS function names were not usable as evidence.
- Workaround: used the `DevTools:RDP Actor` markers (`pagestyle:getApplied()`) instead. It would help if the tool flagged stacks whose frames look inconsistent (JIT frames with raw addresses mixed in), or said when JIT frame attribution may be unreliable.

## marker info --json with handles from different threads

- Command: `profiler-cli marker info m-4 m-107 --json --session …-2` (m-4 on t-131, m-107 on t-0, after `thread select t-131`)
- Expected: a JSON array of two marker records, like the same call with handles from session 1 returned.
- Got: a top-level value whose items were strings, not objects (my `r.get(...)` failed). I did not dig into it; running `marker info m-107 --json` alone worked.
