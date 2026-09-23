## review-browser_html_tooltip-02.js

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SHjPFSkrSEu2sNOU98cErA/runs/0/artifacts/public/test_info/profile_browser_html_tooltip-02.js.json' --session ...-3`
- Expected: the parent process GeckoMain (t-0) selected, as with the profiler link. Got: t-14 (GeckoMain, WebExtensions) selected, so the following `thread markers --category Test --search ...` silently returned 0 markers.
- Workaround: `thread select t-0` after every raw-URL load.
