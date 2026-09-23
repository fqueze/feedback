## Question: which DOM change's stack went through a given function (here, the `requestAnimationFrame` callback in `Tabbrowser.sys.mjs!addTab` that sets `fadein`)

- Command: `profiler-cli thread markers --session <s> --search "name:SetNeedStyleFlush" --list --limit 0 --json`, a script to pull the handles, then `profiler-cli marker info m-723..m-727 m-1314..m-1445 --session <s> > file` and `grep addTab/<` in the 3,500-line output.
- What could have answered it directly: a stack filter on marker lists, e.g. `thread markers --search name:SetNeedStyleFlush --stack-search "addTab/<"`, printing the matching markers with their times.

## Question: what image did the test's `drawWindow` screenshot contain

- Command: `profiler-cli marker info m-62 --session <s> --json`, a regex to pull the `data:image/png;base64,...` URL out of the `Image Load` marker, then a hand-written PNG decoder (no PIL on the machine) to print the 16x16 pixels.
- What could have answered it: `marker screenshot` (or a sibling) accepting an `Image Load` / `Image Paint` marker whose URL is a `data:` image and writing it to a file, as it already does for `CompositorScreenshot`.

## `screenshots` gives 350x259 images of a 1280x949 window, with no way to enlarge a region

- Command: `profiler-cli screenshots --session <s> --range 46.92,47.0 -o shots1`
- Expected: some way to look at a 30px-tall tab strip, e.g. `--crop x,y,w,h` and `--scale N`.
- Got: whole-window JPEGs at 350px wide, where the tab strip is unreadable.
- Workaround: `sips -z 1036 1400` to upscale, then `sips -c 120 560 --cropOffset 1 1` to crop the top-left corner (`--cropOffset 0 0` silently crops the centre instead).

## Question (review): which marker does a link's `marker=N` select

- Command: `profiler-cli marker info m-82 m-73 m-269 … --session <s> --json`, and a script to print each record's `markerIndex`, for every link in the report (about 25 links over four profiles).
- What could have answered it: the default `marker info` output printing the marker index next to the handle (`Marker m-269 (index 276191)`). Or a way to go from index to handle (`marker info --index 276191`), so a link can be checked without first finding the marker by search.
