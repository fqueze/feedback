## Question: "which JS code dirtied style in this range?"

- Command: loop of `profiler-cli marker stack m-N` over every `SetNeedStyleFlush` handle in a zoom, grepping for `Sidebar`/`animate`.
- `thread markers --search` matches names and payloads but not stacks, so finding the `handleToolbarButtonClick` / `Element.animate` markers took one call per marker. A `--stack-search <fn>` filter on `thread markers` would have answered it in one call.

## `screenshots` has no list-only mode

- Command: `profiler-cli screenshots --range 2.9,9.3 -o /dev/null --session ...`
- Expected: the list of screenshot timestamps. Got: `Error: EEXIST: file already exists, mkdir '/dev/null'`.
- Workaround: write the images to a scratch directory and read the list from the output.

## Question: "the test's log in a profile loaded from its raw Taskcluster URL" (review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_chat_page-2.js.json --session S`, then `thread markers --session S --category Test --search browser_chat_page --list`.
- Expected: the parent process main thread selected, as it is when the profiler.firefox.com link is loaded. Got: `t-19 (GeckoMain, Privileged Content)` selected, and `No markers match`.
- Workaround: `thread select t-0`. A mochitest profile's default thread would be better as the parent main thread.
