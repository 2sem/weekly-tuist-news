# Weekly Tuist News #1

*A weekly roundup of what's happening in the Tuist ecosystem — release notes, community highlights, and notes from actually using it.*

Published: Medium, by Lee young-jun (toyboy2) — https://toyboy2.medium.com/weekly-tuist-news-1-da9f701453e5

> Note: this is a local archive fetched from the published Medium article. Inline screenshots ("Press enter or click to view image in full size") are not included — text only.

---

### Who's writing this

I'm Youngjun Lee (Gamehelper), an iOS engineer. I contributed to Tuist's Korean localization, and I'm listed as a testimonial on Tuist's official site. I don't use Tuist in a company production codebase today (my current and past teams have been React Native or hadn't found the right moment to adopt it), but I run it in every personal project, and I've been active in the public Tuist Slack for a while now.

This series is my way of tracking Tuist closely and sharing what I learn — not a claim to any official role.

A note on cadence: Tuist doesn't ship on a strict weekly rhythm, so this debut issue catches up on the most notable changes from the past couple of weeks rather than a strict "last 7 days." Starting with issue #2, each issue will cover the period since the previous one — shorter when there's less to report, longer when there isn't.

### Last week's changes

**1. SwifterPM is now the default dependency restoration method**

As of 4.202.0, `tuist install` uses SwifterPM by default to fetch and restore Swift package dependencies. Resolution still goes through Swift Package Manager, but restoration now links checkouts to a global content-addressable store with symbolic links instead of copying them into every worktree. The practical effect: warm restores drop to sub-second, and the duplicated-gigabytes problem across worktrees goes away. This used to be opt-in — now it's on by default, with a fallback to the environment variable (`TUIST_USE_SWIFTERPM=0`) if you hit an unsupported package.

Tip: in CI, you can register `~/.cache/swifterpm` as a cache path.

**2. Controlling target cache invalidation conditions**

A target's cache can be affected by more than source files and declared dependencies — code-gen templates, tool versions, config files, environment variables. Until now, changes to that environment wouldn't invalidate Tuist's cache. As of Pre-release 4.204.0, you can now control this by setting `additionalHashingInputs` on a target:

```swift
let hashingInputs: [Target.HashingInput] = [
  .glob("Templates/Model.stencil"),
  .glob("Codegen"),
  .glob("Config/**/*.json"),
  .environmentVariable("FEATURE_CONFIGURATION"),
  .string("generator-v2"),
  .script("codegen --version"),
]

let target = Target.target(
  ...,
  additionalHashingInputs: hashingInputs
)
```

**3. Coding agents can now authenticate directly with Tuist (auth.md)**

News from July 17. Tuist now supports `auth.md` for coding agents connecting through its MCP server (currently supporting Claude Code, Codex, OpenCode, and Pi). An unauthenticated agent can discover the auth flow on its own, register, request a six-digit approval code, and receive a scoped credential once you approve it. Once connected, the agent can list your accounts, create a project, guide a Gradle integration, and verify cache behavior, then report back that setup is done.

Why it matters: as more iOS/Android teams bring coding agents into CI and local development, having a standardized auth flow for AI agents is a meaningful improvement on the security side.

### Tuist Talk

Tuist put out a call in Slack for anyone willing to test their GitHub macOS runner (`tuist-macos`) to DM them. I got invited and gave it a try, but it didn't work.

After checking in, I was told to try it from an Organization — but I can't move my project into one, so it's still not usable for me at the moment.

If you want to try it yourself, change your runner as below and reach out by DM or comment.

```yaml
runs-on: tuist-macos
```

### Hands-on notes

**SwifterPM**

After seeing that SwifterPM was now the default, I was curious whether I could use it in my own project, and how much faster it would actually be.

To get a comparison, I ran a GitHub deploy from my existing branch, then tried again on a branch where I'd upgraded Tuist.

The build failed. Trying to build locally to track down the issue, I got errors saying none of the Core Data models could be found.

The model files were missing from Resources. The cause turned out to be something from an earlier fix: to resolve a "Multiple commands produce" error, I had excluded the `.xcdatamodel` file inside the `.xcdatamodeld` from the Project Manifest. Since a recent update had changed the schema, that `.xcdatamodeld` now had two files, `Model.xcdatamodel` and `Model2.xcdatamodel`, and excluding only `Model.xcdatamodel` fixed it.

Still, it didn't feel clean, so I tried a different option. A `coreDataModels` setting had actually been added earlier for exactly this kind of issue, but it hadn't solved my problem when I first tried it. Keeping the `.xcdatamodeld` excluded from Resources,

```swift
resources: [.glob(pattern: "Resources/**",
  excluding: [
    ...,
    "Resources/Databases/DAModel.xcdatamodeld/**"
  ]
)],
```

and specifying the excluded `.xcdatamodeld` under `coreDataModels`,

```swift
coreDataModels: [
    .coreDataModel("Resources/Databases/DAModel.xcdatamodeld")
]
```

made the error go away.

In the past, when I hit an error like this, I'd dig into which version or which commit introduced it. This time I didn't go that far.

After fixing the build error, I checked the deploy time — it hadn't changed at all, so I went back to investigating locally. SwifterPM's docs say a cache gets created at `~/.cache/swiftpm`, and to register that path as a cache, but nothing was actually being created there.

Even after clearing both the Tuist cache and the SPM cache entirely, it still didn't work, which led me to a hypothesis: maybe it only kicks in when you're using `Package.swift` directly. I pulled up a Tuist sample project I'd made earlier and ran `tuist install` — the log showed `swifterpm`.

After running clean and then install again, the log showed it being restored from `~/.cache/swiftpm` instead of being fetched.

Based on the description that "if you integrate with an existing Xcode project, `tuist install` uses SwifterPM," I expected it to work in Tuist-generated projects too — but it turns out it only works in projects that use `Package.swift`.

**Testing auth.md**

To test this, I first logged out of my existing session.

```bash
mise x -- tuist auth logout
```

When I asked an agent to run `generate`, I got an error telling me to log in.

After installing the Tuist MCP,

```bash
claude mcp add --transport http tuist https://tuist.dev/mcp
```

trying again got me this response:

> Still unauthenticated. A Tuist MCP auth tool just became available — trying that instead of the browser flow:
> That MCP tool authenticates the MCP server, not the CLI — it won't write the `~/.config/tuist/credentials` entry that `tuist generate` reads. Wrong lever; skipping it.
> Checking whether the auth requirement can be sidestepped for a plain generate:
> Ran 1 shell command
> Can't be worked around from here. Tuist.swift declares `fullHandle: "gamehelper/democracy-action-ios"`, so the project is server-connected and every generate authenticates first — `--no-binary-cache` fails identically.
> The login is an interactive browser flow, so it has to come from your terminal:
> `! mise x -- tuist auth login`
> Ping me after and I'll run the generate.

It says MCP authentication is required.

Even after authenticating the MCP, it still asked me to log in — so I concluded this doesn't automate authentication for Tuist builds. (That was apparently explained somewhere, but I don't think I read it carefully enough.)

After logging out of the MCP and asking for a list of projects, I was asked for my email address. Once I gave it, an approval code was issued and I was told to open a page and enter it. On that page I was asked to enter a six-digit code, and once I completed the authentication, Claude picked it up and correctly showed the project list.

But the MCP still showed as failed. When I authenticated again, I saw it open a browser, authenticate, and close automatically.

Since it worked right after logging out of the MCP, I expected this authentication to connect the MCP — but the MCP still showed as disconnected, even though calls were succeeding normally, which was confusing. My guess is that when you authenticate through auth.md, the agent holds a token but the CLI doesn't, which is why this happens.

### Closing

Happy Coding! 😎

If you found this post helpful, please give it a round of applause 👏. Explore more iOS, Tuist related content in my other posts.

For additional insights and updates, check out my LinkedIn profile. Thank you for your support!
