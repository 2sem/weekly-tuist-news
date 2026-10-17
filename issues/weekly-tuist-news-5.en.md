# Weekly Tuist News #5

> Note: this is a draft. Translated from the approved Korean draft. Not yet published to Medium.

---

This issue is mostly fixes that tighten cache accuracy and generation speed, rather than headline features. On the other hand, the Xcode 27 `project.xcproj` option, which only existed in canary builds, landed in the stable CLI with 4.210.0. On the dashboard side, there were updates around cache transfer volume and free-tier usage.

**1. CLI 4.210.0 (9/28): automated Monday releases, and an option for Xcode 27's project format**

GitHub Actions in the Tuist repository is now automated to promote the RC to stable every Monday and then cut the next RC, so the CLI release cadence has gotten faster.

On the feature side, a `projectFormat` generation option was added to turn on Xcode 27.2's JSON5-based project format (`project.xcproj`) from `Tuist.swift`. Also in this release: a performance improvement that hashes the graph once instead of twice during a cache warm, and a fix for underscores (`_`) disappearing from package bundle identifiers.

Source: [4.210.0 release notes](https://github.com/tuist/tuist/releases/tag/4.210.0)

**2. CLI 4.211.0 (10/5): cache key and module cache accuracy**

Two features were added. One is the move of the modern module cache to REAPI (Remote Execution API); the other is Linux runner cache volumes that work across CI providers. The rest is mostly fixes.

- The Xcode build is now included in binary cache keys.
- warnings-as-errors settings no longer leak into the cache key hash.
- Module cache downloads on slow networks now resume instead of being cut off (previously they were capped and dropped).
- Fixed silent misses in the Xcode cache caused by entries removed by eviction.
- The toolchain is now part of the selective testing hash, and test modules skipped by selective testing are shown in the job summary.
- In report mode, a target with a flaky new test is now kept re-running. This is a fix to `--stress-new-tests`, which we covered in issue #4.

Source: [4.211.0 release notes](https://github.com/tuist/tuist/releases/tag/4.211.0)

**3. CLI 4.212.0 (10/12): generation speed and cache restore fixes**

- Generation speed: side effects in the generation step now run concurrently, quadratic link checks are gone, and `StaticXCFrameworkModuleMapGraphMapper` no longer walks every target's closure. A slowdown in generate caused by signature verification on local cache hits was also fixed.
- Cache restore: fixed cached targets whose outputs include an empty file not being restored, module cache stalls on a 4.211 cold generate, and dSYMs not being bundled into XCFrameworks warmed for release.
- Other: Swift warning group flags are preserved, selective testing now runs correctly for schemes with hand-written test plans, and code coverage is kept when Xcode 27 cache prefix mapping is on.

Source: [4.212.0 release notes](https://github.com/tuist/tuist/releases/tag/4.212.0), [4.211.1 release notes](https://github.com/tuist/tuist/releases/tag/4.211.1)

**4. The Xcode cache now only exchanges what changed**

Previously, changing a few lines of source could cause the entire compiled output to be uploaded again. That has been improved. The Xcode cache now uses content-defined chunking: on upload, only the chunks the server doesn't already have are sent, and on download, chunks already on disk are reused and only the missing pieces are fetched to reconstruct the original. This local chunk cache survives wiping `DerivedData` or restarting the cache service.

In Tuist's own published benchmark (on generated fixtures), editing a single C function cut that object's transfer volume by 47.4%, and renaming a single property in a large Swift module cut it by 27.8%. This is measured in bytes transferred, not a claim that build time drops by the same amount, and the compiler's invalidation behavior is unchanged.

Source: [Reuse unchanged chunks when sharing Xcode build outputs](https://tuist.dev/changelog/2026.09.08-xcode-cache-chunk-reuse)

**5. Air plan: you get an email as you approach your limits**

Last issue we reported that cache usage limits on the free (Air) plan had started to be enforced. Now there's a heads-up before you hit the limit: organization admins and personal account owners get an email at 80% and 100% of monthly cache or runner usage. The email includes current usage, the reset date, and a Pro upgrade link.

Source: [Get an email before reaching your Air limits](https://tuist.dev/changelog/2026.09.09-air-usage-emails)

### Tuist Talk

On [Slack](https://tuist-community.slack.com/archives/C018QG7U7SN/p1789582255849589), a user brought this up with "this could be interesting," and Pedro replied that today's models already understand the format and structure of `.pbxproj` well. The user said it's still interesting, but that as usual there will be bugs for an Xcode version or two and it won't have every pbxproj feature. Pedro answered that he was already working on Tuist support and that it would land within a day or so.

Indeed, [shortly after](https://tuist.dev/changelog/2026.09.17-xcode-27-project-format), starting with 4.210.0, you can turn this format on through a generation option in `Tuist.swift`.

```swift
import ProjectDescription

let tuist = Tuist(
    project: .tuist(
        generationOptions: .options(
            projectFormat: .xcproj
        )
    )
)
```

That said, the format itself is still experimental in Xcode. Versions before 27.2 can't open `project.xcproj`, so to turn it on, your local machines and CI runners all need to be on 27.2 or later. The default is still `.pbxproj`, so nothing changes unless you turn the option on.

### Practice

I compared timings for 4.207 and 4.212 on the same project, using Xcode 27.1.

**4.207**
- install: 18s
- cache: 54s
- generate: 2.7s
- clean build: 7.5s

**4.212**
- install: 10.2s
- cache: 43s
- generate: 3.3s
- clean build: 3.7s

install, cache, and clean build got faster, while generate actually went up from 2.7s to 3.3s.
