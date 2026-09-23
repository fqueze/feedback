## `profile info` / the view header give no hint that the buffer only covers the last ~3s

- Command: `profiler-cli profile info --session browser-support_firefox-1` (Oh2BGlUlRFuFoDLvDUfizg, per-test profile of browser_homepage_firefox_home_support_firefox.js)
- Expected: a warning that samples and short markers only start at ~6m36s of a "Full: 6m40s" profile. The buffer was full: 6.9M markers, 99% `DocAccessible::ContentRemovedNode`.
- Got: "Full: 6m40s" and 2.8s of CPU, with nothing saying the first 6m36s were evicted. I noticed only because `--category Test` returned 8 markers, all from the test's last second.
- Workaround: listed markers of several types to find the earliest remaining one.
- Suggestion: print "data covers <t0>–<t1>" per thread (first/last sample, earliest short marker) when it is much shorter than the profile's range.

## Question: "machine CPU use during one test" (resource-usage profile)

- Command: `profiler-cli zoom push m-3; profiler-cli thread markers --search "name:CPU Use"`
- The aggregate view gives the marker count and interval durations, but not the distribution of the `CPU Percent` field.
- Workaround: `--list --limit 0 --json` piped to a Python script to compute the mean, median and max of `fields[CPU Percent]`.
- What would have answered it: per-field numeric stats (mean/median/max) in the aggregated `thread markers` output for numeric payload fields, or a `--stat <field>` option.

## Question: "how many top-level calls of a recursive marker, and how long each took"

- Command: `thread markers --search name:DocAccessible::PruneOrInsertSubtree --min-duration 1 --list`
- The list shows 58+ markers with near-identical durations (nested recursion frames), which buries the 4 top-level calls under their nested copies.
- Workaround: `--json` and a script that merges markers contained in the previous one.
- What would have answered it: a `--top-level` / `--no-nested` filter keeping only the markers not contained in another of the same name on the same thread.

## Default thread on load is not always the parent main thread

- Command: `profiler-cli load <profile_browser_homepage_firefox_home.js.json of Ff7vCWhaRBK1bbmqqFhErA>` followed by `thread markers --search name:Runnable ...`
- Got: t-12 (GeckoMain, Privileged Content) was selected, so the first queries returned 0 or unrelated markers. The header does say so, but it is easy to miss after `load`.
- Workaround: `thread select t-0`.
