## Question: which document is this marker for?

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338"` (and `--search 4294967338`) on the WebExtensions GeckoMain thread
- Expected: the markers carrying that innerWindowID, e.g. the DOMEvent load of the document whose refresh driver emits "RefreshDriverTick waiting for paint"; ideally also the page URL for that innerWindowID from the profile's pages table.
- Got: "No markers match", although `marker info` shows `innerWindowID: 4294967338` under "Other payload fields (no schema)" on both the RefreshDriverTick and DOMEvent markers. There is no command that maps an innerWindowID to its page URL.
- Workaround: listed the DOMEvent load markers around the right time and ran `marker info` on each until the innerWindowID matched.
- What would have answered it: `--search` matching schema-less payload fields, and the page URL printed next to innerWindowID in `marker info` (or a `profile pages` command).
## Question (review): where does profiler-cli keep its sessions in this sandbox?

- Command: `PROFILER_CLI_SESSION_OWNER=… profiler-cli load <url> --session …`
- Expected: the session starts, as the brief assumes.
- Got: EPERM creating `~/.profiler-cli`. The error message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked. Neither the brief nor the guide mentions this variable.
- Workaround: set `PROFILER_CLI_SESSION_DIR` on every call, next to `PROFILER_CLI_SESSION_OWNER`.
- What would have answered it: falling back automatically to the writable directory it already suggests.
