
## Review (browser-review-browser_pageinfo_images.js), 2026-09-22

- **Question: when did this request's response arrive, on the timeline I am reading?** `profiler-cli marker info m-26 --session browser-review-pageinfo_images-2` on a Network marker prints `Time: 6.332s - 7.685s`, then raw payload fields `startTime: 6341.92`, `endTime: 7695.07`, `responseStart: 7574.92`, `fetchStart: 6815.36`. Those use another zero (about 9.6 ms off) and are in ms. Expected: the timing fields in the same seconds as `Time:`, e.g. `responseStart: 7.565s`, which would let you compare them with the other markers directly. Workaround: subtract `startTime` minus the displayed start from each field by hand.
