## Question: how long did each IPC message wait in the receiver's queue?

- Commands: `profiler-cli marker info m-4736` (IPCIn on the parent main thread, sender process not in the profile), then a script over `thread markers --search name:IPCIn,name:IPCOut --list --limit 0 --json` (`ipcdelay.py` in this directory).
- What I needed: for each message from the test's content process, when the parent's IO thread received it and when the parent main thread handled it. The parent main thread was blocked for 142 ms, and the diagnosis rests on the Destroy/Cancel messages being received at 8.482 s but handled at 8.623 s.
- Got: when the sender is not profiled, the IPCIn marker is shown at its handling time with duration 0; the receipt time is only in the unlabelled payload field `recvEndTime`, and in a different time base from the displayed times (`endTime` 8630.56 for a marker displayed at 8.6229 s: a 7.62 ms offset I had to derive per marker). When the sender is profiled, the same marker is shown at the send time with a duration up to handling, so the two cases read differently.
- What the output could show: a "queued for X ms (received t=…, handled t=…)" line on IPC markers, in profile-relative seconds, in both cases.

## `thread markers --list --limit 0 --json` over a full parent main thread does not finish in 2 minutes

- Command: `profiler-cli thread markers --session <s> --thread t-0 --search "name:IPCIn,name:IPCOut" --list --limit 0 --json` with no zoom, on a parent main thread with 888,768 markers.
- Expected: a few seconds, or a warning.
- Got: still running after 120 s; I killed it. After `zoom push` to the 0.3 s of the test it returned at once.
- Workaround: always zoom first.

## Question: what was the thread doing during this one marker? (`--during-marker --search` also filters the stacks)

- Command: `profiler-cli thread samples-top-down --session <s> --thread t-0 --include-idle --during-marker --search "name:Runnable,SetCharacterMap"`
- Expected: the call tree of the samples that fall inside `Runnable` markers matching `SetCharacterMap`.
- Got: `Search: "name:Runnable,SetCharacterMap"` and `Filters: [~] during marker matching: "name:Runnable,SetCharacterMap"`, then "No samples matched --search". The one `--search` was used twice, as the marker filter and as a stack-frame search, and no frame is called `name:Runnable`.
- Workaround: `zoom push m-N` on the marker, then `thread samples-top-down --include-idle`, then `zoom pop`.
- What the output could show: a separate flag for the marker filter's search, or a warning that `--search` is being used as a stack search too.
