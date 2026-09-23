## "In which order did these markers happen?" — list times are rounded to the second

- Command: `profiler-cli zoom push 590.9,591.5 --session S` then `profiler-cli thread markers --category Test --list --limit 0 --session S`
- Expected: start times precise enough to order markers within a 600 ms zoom (e.g. `591.316s`).
- Got: every row shows `t=9m51s`, even zoomed to 600 ms. The race I was diagnosing is between markers 3–30 ms apart on two threads, so the text list could not order them.
- Workaround: `--json` and a script printing `start/1000` with ms precision (`ml.py` in this directory). Needed on every list I read.
- What the output could have shown: `t=591.316s` (or ms resolution whenever the zoom or the list spans less than a few seconds).

## "Which document does each of these markers belong to?" — innerWindowID not in the list

- Command: `profiler-cli thread markers --thread t-123 --search "DOMEvent,Navigation,..." --list --limit 0 --session S`
- Question: which of the `DOMWindowCreated` / `unload` / `DOMInteractive` rows are the initial about:blank of `#aichat-browser` and which its about:aichatcontent document.
- Got: no innerWindowID in the list rows; had to run `marker info m-N --json` on each marker and read `rawFields`.
- What the output could have shown: an `innerWindowID` (or URL) column in `--list`, or a `--group-by field:innerWindowID` hint.

## "For each occurrence of A, when did the next B on another thread happen?" — scripted

- Question: for every AI window a test opened, how many ms after `openNewBrowserWindow` resolved (parent) did `AIChatContent:Ready` fire (Privileged Content). That gives the race margin of passing runs.
- Commands: three `thread markers --search ... --list --limit 0 --json` calls merged in `race2.py` here.
- What the output could have shown: no existing command answers it; a cross-thread "time from each A to the next B" would.

## "When was this IPC sent?" — `marker info` prints raw child-clock payload times next to shifted marker times (review)

- Command: `profiler-cli marker info m-1 --session S` on an `IPCIn` marker (`PWindowGlobal::Msg_RawMessage`) of the Privileged Content main thread, Ls085ylPT9inKrvaOf0BAA.
- Expected: one timeline. The parent's `SendQuery` for that message is at 1006.764, and so is the marker's own start.
- Got: under "Other payload fields (no schema)", `startTime: 1006771.27`, `sendStartTime: 1006771.27` etc., 7 ms off, because they are not shifted to the merged timeline. The original report took 1006.771 as the send time, and that wrong figure ended up in its race margin (+50 instead of +43).
- Workaround: compare with the sender's `SendQuery`/`IPCOut` marker.
- What the output could have shown: payload times shifted like the marker's (or hidden), or labelled as raw process-local times.
