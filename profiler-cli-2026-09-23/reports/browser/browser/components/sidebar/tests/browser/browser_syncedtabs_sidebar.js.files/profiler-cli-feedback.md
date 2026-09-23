## Question: at what exact time was each screenshot around the wait taken?

- Command: `profiler-cli marker info m-3560..m-3580 --session <s> --json`
- Expected: each record carries its marker handle, so start times can be matched to handles such as m-3574.
- Got: `handle` is missing (None) from every record. Also, `screenshots --range` prints times rounded to the second (`t=1m41s`), which cannot say whether a frame came before or after a wait that starts at 100.576 s.
- Workaround: worked out the handle from the record's position in the range.
## Question: in which order did these markers happen, a few ms apart? (review)

- Command: `profiler-cli thread markers --session <s> --search name:SetNeedStyleFlush --list --limit 60` inside a 14 ms `zoom push`
- Expected: each row's start time to the 0.1 ms, since the zoom is 14 ms wide and the question is whether a DOM insertion came before or after a wait that starts at 100.576 s.
- Got: every row says `t=1m41s`.
- Workaround: passed the handles to `marker info <handles> --json` and printed `start` with a script.
