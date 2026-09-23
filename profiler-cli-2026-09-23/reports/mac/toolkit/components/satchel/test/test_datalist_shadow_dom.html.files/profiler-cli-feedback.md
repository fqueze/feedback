## How often was this thread actually sampled?

- Command: `profiler-cli profile meta --session test_datalist_shadow_dom.html-1` prints `Sampling interval: 10ms`.
- Expected: some way to see that the effective rate differs from the configured one.
- Got: the configured interval only. The parent main thread of this macOS 15 CI profile has 291 samples over 14s, about one every 50ms, and 26 in a busy 1.3s range. I found that out only by running `zoom push <range>` + `counter info c-1` (its `Samples: N in current range` line) over several ranges.
- What the output could show: `profile meta` or `thread info` could print the effective median sample spacing next to the configured interval, and flag a large gap between them. It matters when reading CI profiles: sparse samples change what "idle for 170ms, 3 samples" means, and here it was also evidence that the OS was waking the sampler late.

## Which of two markers on different threads came first? (review-test_datalist_shadow_dom.html)

- Command: `profiler-cli thread markers --list --limit 0 --search "AutoComplete,SpecialPowers,setTimeout" --session review-test_datalist_shadow_dom.html-2`, on the parent (t-0) and then the content thread (t-15).
- Expected: enough time precision to order a parent `setTimeout() for [anonymous]` runnable against the content's `nsAutoCompleteController` runnable and `SendQuery AutoComplete:StartSearch`.
- Got: all of them print `t=3.737s`/`t=3.738s`. The order (content timer 3737.153ms, parent timer 3737.179ms, parent reply 3737.325ms, content StartSearch 3737.514ms) came only from `marker info <m> --json` piped through a script, one handle at a time.
- What the output could show: a `--precise`/`--time-format ms` option for `--list`, or more digits whenever neighbouring rows round to the same value. Cross-process races depend on sub-millisecond ordering.
