## Question: which XPCOM component a `StaticComponents.cpp:<line>` warning names

- Context: CI logs `NS_ENSURE_TRUE(inst) failed — StaticComponents.cpp:15659` twice during this test's shutdown (JUfa5PoLQumisksWau_HjA, V88XM6eQRIqGFS-v_rs5Bw). The file is generated, so hg has no copy to read.
- What I did: read the build task id from the test task's dependencies, downloaded its 21 MB `public/build/target.generated-files.tar.gz`, and extracted `xpcom/components/StaticComponents.cpp` to read the line (it is `nsUserIdleServiceWin::GetInstance()`).
- What could answer it: `fx-tests task <id>` could print the build task id (and the generated-files URL), or a helper could resolve `<generated file>:<line>` for a job's build.
