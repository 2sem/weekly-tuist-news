# Weekly Tuist News #4

> Draft — pending review

---

This issue got pushed back — there wasn't much to report for a while, then I got busy. In the meantime the CLI shipped two stable releases (4.206.0, 4.207.0), with a lot of stability-focused work.

**1. CLI 4.207.0 — cache connectivity switches to a new default**

Running `tuist setup cache` now installs a **machine-wide CAS (Content-Addressable Storage) proxy + plugin** by default, instead of the per-project cache daemon. Newly generated projects also get this CAS plugin build setting by default, instead of the old `COMPILATION_CACHE_REMOTE_SERVICE_PATH`.

If something breaks, the `TUIST_FEATURE_FLAG_KURA=0` environment variable reverts to the old behavior (legacy daemon + old build setting). You can also set it per environment — e.g. keep the new path on CI while local dev machines stay on the old one.

Also in this release: xcresult bundles whose names don't end in `.xcresult` are now recognized, and a bug in `xctestrun`-based selective testing support was fixed.

Source: [4.207.0 release notes](https://github.com/tuist/tuist/releases/tag/4.207.0)

**2. CLI 4.206.0 — Air free-tier cache usage limits now enforced**

Usage limits for the free (Air) plan now apply across live cache lanes. If you'd effectively been using the cache without limits, you may notice the difference now. Also shipped: `tuist generate` now logs the binary cache configuration currently in effect. Bug fixes include: visionOS UI test targets were blocked from depending on things they shouldn't have been; release builds' DerivedData path changed between runs, defeating the compilation cache; Objective-C resource accessors were only being generated for Swift.

Source: [4.206.0 release notes](https://github.com/tuist/tuist/releases/tag/4.206.0)

**3. Stress-test the tests your branch adds before merging**

This feature reruns only the newly added test cases in a PR several times before merge, to catch flaky tests early. Fast tests get up to 10 reruns, slower ones fewer, and the whole pass stops at 200 test cases or 10 minutes, whichever comes first. `report` mode just warns; `enforce` mode fails the run.

```
tuist xcodebuild test --stress-new-tests report -scheme MyScheme
```

Or enable it via the `TUIST_TEST_STRESS_NEW_TESTS` environment variable.

Source (PR): [Stress-test the tests your branch adds](https://github.com/tuist/tuist/pull/12809)

### Tuist Talk

The official blog published ["The physics of build systems"](https://tuist.dev/blog/2026/08/25/the-physics-of-build-systems). It covers why build graphs, incremental builds, remote caching, and remote execution decide whether a build feels fast.

On [Slack](https://tuist-community.slack.com/archives/CCYNEGY1L/p1788506463708969), a user open-sourced their side project and asked whether it was okay to make its Tuist Dashboard public too. Pedro was delighted, and mentioned he's discussing a feature with Marek Fořt to let projects register a logo so the Open Graph image reflects it when a dashboard link is shared on social media.
