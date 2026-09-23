## Question: was the machine saturated only when the test failed, or through the whole parallel phase?

- Profile: a resource-usage profile (`.../YmbfiYOmQ8Wn4Hdscl3t5A/runs/0/artifacts/public/test_info/profile_resource-usage.json`).
- `profile info` says "No significant activity" and `counter list` says "No counters in this profile", although the profile has 1,895 `CPU Use` markers carrying the machine's CPU percent.
- `thread markers --search 'name:CPU Use' --list` rows show the time and duration but not the CPU percent; `marker info` on each shows it, one at a time.
- Needed a script over `thread markers ... --list --limit 0 --json` to bucket `cpuPercent` per 30 s (answer: 100% from 60 s to 330 s, the whole parallel phase; ~25-40% during the sequential retry).
- Could have shown: the `CPU Use` markers as a counter (so `counter info` gives "over time" buckets under a zoom), or the CPU percent in the `--list` row.

## Question (review): which IPC messages are these?

- Command: `profiler-cli thread markers --list --limit 0 --session <s>` on `.../Xg4olA56TYy-Bkul-bStzQ/.../profile_test_extension_storage_actor_upgrade.js.json`, zoomed to 600–720 ms.
- Expected: each `IPCOut`/`IPCIn` row to carry its message type, as `Runnable` rows do.
- Got: blank description column for every IPC row; had to `marker info` each one to learn that two were `PBackgroundIDBDatabaseConstructor` / `PBackgroundIDBVersionChangeTransactionConstructor`.
- Could have shown: the `Type` field (message name) in the `--list` row.

## Question (review): was the main thread already inside `nsIFile.create` before a given marker?

- Profile: `.../YmbfiYOmQ8Wn4Hdscl3t5A/.../profile_test_extension_storage_actor_upgrade.js.json`, marker `NewThread QuotaManager IO` at 2462.04 ms.
- `thread samples*` give aggregated trees without sample times, so I bisected with `zoom push 2.4613,2.4620` / `2.4613,2.4640` and read the tree each time to find that the first `create` sample preceded the marker.
- Could have shown: a per-sample list (time + leaf/stack) for the zoomed range, e.g. `thread samples --list`.
