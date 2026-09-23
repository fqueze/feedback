## Loaded profile's default selected thread is not the parent GeckoMain
- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ct9m-aJqRqGxz_JUAX_2mQ/runs/0/artifacts/public/test_info/profile_browser_device_modal_submit-2.js.json --session browser_device_modal_submit.js-2`
- Expected: t-0 (GeckoMain, Parent Process) selected, as with the first profile of the same job.
- Got: t-11 (GeckoMain, WebExtensions) selected; `thread markers --category Test --search ...` then returned "0 markers" with no hint that the wrong thread was selected.
- Workaround: `thread select t-0`.

## review-browser_device_modal_submit.js
- Command: `profiler-cli load <raw Taskcluster URL of profile_browser_device_modal_submit-2.js.json> --session …-3`
  Expected: parent-process GeckoMain selected, as when loading the `thread=0` profiler link of the same profile. Got: `t-11 (GeckoMain, WebExtensions)` selected; a `--search` then returned 0 markers with no hint that the wrong thread was selected.
  Workaround: `thread select t-0`.
