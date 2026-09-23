## Full-profile view drops almost all samples (review, 2026-09-22, profiler-cli 0.9.0)

- Command: `profiler-cli thread samples --session <s> --include-idle` right after
  `profiler-cli load <profiler.firefox.com from-url link, marker-table, no range>`, status "View range: Full profile".
- Expected: every sample of the parent main thread (Linux profile a5EvEYgZSy-ge0YS3Kle5Q, 70 s).
- Got: 30 samples; `thread functions --search moz-support-link` found nothing ("filtered from 96").
  After `zoom push 0,70.5`: 6488 samples, and the functions were there. Same on the Windows
  profile d-Vz3n8BToGDYJyyg5DHGA: 7 samples full view, 613 with `zoom push 0,10.45`.
- Workaround: explicit `zoom push 0,<duration>` before any samples/functions query. Without
  it, "no samples match" reads as evidence of absence.

## profile-link.py refuses a session loaded the way review-brief.md says

- Command: `profile-link.py --session <s> --marker m-243`, session loaded with
  `profiler-cli load <profiler.firefox.com link from the report>` as review-brief.md instructs.
- Expected: a link.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
- Workaround: loaded the raw Taskcluster URL into a second session just to build one link
  (another 0.6 GB load). The review brief should say to load the raw URL, or the script should
  accept a from-url link.
