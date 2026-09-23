
## (review) A marker's cause stack reads as its own stack
- Command: `profiler-cli marker stack m-287 --session review-browser_contextmenu_chrome_images.js-1` (a `RefreshDriverTick` at 12.794 s; same in `marker info`)
- Expected: the stack labelled as the marker's cause, i.e. what started the refresh timer, with how long before the marker it was taken.
- Got: `Captured at: 12.747s`, then a stack under an earlier tick. Nothing says it is a cause stack 47 ms older than the marker, so it reads like a wrong stack, or like this tick ran from `NotifyVsyncTimerOnMainThread`, while it actually ran from a `nsRefreshDriver::FinishedWaitingForTransaction` runnable.
- Workaround: listed the `Runnable` markers around the tick to find what ran it.
