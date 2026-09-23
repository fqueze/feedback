## Question: which document did these DOM changes happen in?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>`
- Expected: the list rows to show something that tells documents apart (innerWindowID, or better
  the document URL / tab), since a Privileged Content process hosts both the test's about:newtab
  and the preloaded about:newtab.
- Got: rows show only `SetNeedStyleFlush SetNeedStyleFlush`; the innerWindowID is only in `--json`.
  A burst of DOM changes I first took for the test page updating was the preloaded page.
- Workaround: `--json | jq '.flatMarkers[] | "\(.start) \(.data.innerWindowID)"' | uniq -c`.

## `thread markers --category Test --search "Test,..."` matched hundreds of `Preference Read` markers

- Command: `profiler-cli thread markers --search "Test,PushPrefEnv,newtab-top-sites-changed" --list --limit 0`
- Expected: the Test-category markers plus the named ones.
- Got: every `Preference Read` whose pref name contains "test" (payload match), ~160 rows of noise.
- Workaround: `--category Test` separately; worth documenting that a bare term also matches pref
  names in payloads, or offering `category:Test` inside `--search`.

## Question (review-browser_topsites_contextMenu_options.js): which IPC message did this `Runnable PWindowGlobal::Msg_RawMessage` handle, and which parent dispatch sent it?

- Command: `profiler-cli marker info <IPCIn handle> <Runnable handle>` on a Privileged Content thread (Windows asan profile DLH54-npS4y9TzFTLayPeQ).
- Expected: the IPCIn marker to name the runnable that handled it (or the runnable to show the messageSeqno), and its payload times to be on the same timeline as the marker's own start.
- Got: the Runnable carries no seqno. The IPCIn's "Other payload fields" `startTime`/`endTime`/`sendStartTime` are ~15.5 ms later than the marker's displayed start (raw, unadjusted process time), which looks like a contradiction until you subtract the offset.
- Workaround: `thread markers --search Msg_RawMessage --list --json`, then subtract (payload startTime - marker start) from the payload `endTime` and match that to a Runnable's start.
