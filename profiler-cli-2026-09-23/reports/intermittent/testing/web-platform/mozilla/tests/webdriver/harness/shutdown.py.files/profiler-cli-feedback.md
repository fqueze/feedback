
## review-shutdown.py: `zoom push` with minute-second times silently picks the wrong range

- Command: `profiler-cli zoom push --session review-shutdown.py-1 24m29s,26m5s`
- Expected: a zoom from 24m29s to 26m5s (the format `thread markers` prints, e.g. `t=24m29s`), or an error.
- Got: `Pushed view range: ts-1 (24s) to ts-2 (26s) (duration: 2s)`: the minutes were read as seconds and the rest dropped, with no warning. Only the thin `CPU Use` markers in that range showed that it was wrong.
- Workaround: convert to seconds by hand (`1469,1565`).

## review-shutdown.py: profile-link.py refuses a session opened from a report link

- Command: `python3 $D/profile-link.py --session review-shutdown.py-1 --marker m-846`, after `profiler-cli load '<profiler.firefox.com/from-url/... link from the report>'`, as `review-brief.md` says to do.
- Expected: a link for the new marker, so the review can cite new evidence from the session it already loaded.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". The only fix was to reload the 0.6 GB profile from the raw URL.
- Workaround: stop the session and load the Taskcluster artifact URL. The review brief could say to do that, or profile-link.py could take the artifact URL out of the `from-url/` link.
