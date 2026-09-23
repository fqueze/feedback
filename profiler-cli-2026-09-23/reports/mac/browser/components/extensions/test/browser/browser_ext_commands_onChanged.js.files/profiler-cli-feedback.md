# profiler-cli feedback — browser_ext_commands_onChanged.js

## Question: which document does a marker's innerWindowID belong to?

- Command: `profiler-cli marker info m-914 --session browser_ext_commands_onChanged.js-1` on a `RefreshDriverTick waiting for paint` marker (NoPayloadUserData) in the WebExtensions process.
- Expected: the innerWindowID resolved to its page URL, or some command that lists the profile's `pages` table (URL, tabID, embedder).
- Got: only `innerWindowID: 4294967331`. `thread markers --search 'innerWindowID:4294967331'` and `--search 4294967331` both matched 0 markers, so I could not use the ID to find other markers for the same window either.
- Workaround: downloaded the profile with curl and read `processes[].pages` in Python. That showed the ID was `moz-extension://…/popup.html`, which was the key fact of the diagnosis.
- What would have answered it: `marker info` printing the page URL next to innerWindowID, a `profile pages` command, and `--search` matching raw payload fields of schema-less markers.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session …`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message did suggest `PROFILER_CLI_SESSION_DIR`, which worked. That cost one round trip.

## Question: which DOMEvent marker is the `command` event on a given `<key>`? (review-browser_ext_commands_onChanged.js)

- Command: `profiler-cli thread markers --search 'command - key' --list --limit 0 --session review-browser_ext_commands_onChanged.js-2`, zoomed to 6.676–6.713 s.
- Expected: the `DOMEvent` rows that `--list` prints as `command - key@15be0e1a0`.
- Got: 0 matches. The printed label is not searchable, only the name and payload fields are.
- Workaround: `--search DOMEvent --min-duration 20`, which found it because it lasted 26 ms.
- What would have answered it: `--search` also matching the label that `--list` prints.
