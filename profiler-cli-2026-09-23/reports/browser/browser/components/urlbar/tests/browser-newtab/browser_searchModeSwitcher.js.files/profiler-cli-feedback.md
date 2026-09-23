## profile-link.py refuses a session loaded from a profiler.firefox.com link (review)

- Command: `python3 $D/profile-link.py --session browser-review-browser_searchModeSwitcher.js-2 --marker m-17`, after `profiler-cli load '<report's profiler.firefox.com/from-url/... link>'` as review-brief.md says to do.
- Expected: a link for a new observation found while checking the report's links.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stopped the session and reloaded the same profile from its raw Taskcluster URL, then re-found the markers. It costs a second download and load of a profile, just to link one extra marker. profile-link.py could take the from-url parameter out of a profiler link itself.
