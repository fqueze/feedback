## Question: which other tests fail in the same jobs as one failure mode?

- Needed: the 11 jobs of `fx-tests test dom/media/test/test_gfx_sanity_gpu.html --task-ids --issue 2` all had `test_mkv_playback.html` time out, and those 11 are all of that test's timeouts in the window. That co-failure was the key lead.
- Commands: 11 separate `fx-tests task <id> --profiles` calls, grepped, then `fx-tests test dom/media/test/test_mkv_playback.html --task-ids` to check the other direction.
- What would have answered it: `fx-tests test <path> --issue <n> --co-failures`, giving the tests failing in the same jobs with a count ("test_mkv_playback.html TIMEOUT 11/11, test_hw_video_decoding.html FAIL 10/11"), and whether each co-failing test fails in other jobs too.
