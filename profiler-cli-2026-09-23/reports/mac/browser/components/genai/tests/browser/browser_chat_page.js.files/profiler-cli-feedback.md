# profiler-cli feedback (review of browser_chat_page.js)

## "Which URL marker index is this handle?" needs --json

- Command: `profiler-cli marker info m-31 --session S`
- Expected: the marker's index in the profile, the `marker=N` of a profiler.firefox.com link, in the default output. Checking a report's links comes down to that one number.
- Got: name, type, time, thread and fields, but no index. `--json` has `markerIndex`. With one handle the JSON is the marker object itself. With several handles it is `{markers: [...]}`, so one script cannot read both shapes without a special case.
- Workaround: `marker info <handles> --json` piped to a Python one-liner that handles both shapes.

## The screenshots listing gives the window size, not the image size

- Command: `profiler-cli screenshots --range 3.05,3.33 -o DIR --session S`
- Expected: the size column to give the size of the files written.
- Got: `1280px × 949px`, but the JPEGs are 350×259. A 40 px strip at the launcher edge is about 10 px wide in these files. Seeing it needed a crop and a 4x upscale with `sips`.
- Could show: both sizes, e.g. `window 1280×949, image 350×259`.
