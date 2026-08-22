---
name: write-sns-post
description: Use when a Weekly Tuist News issue has just been published and the user wants short social posts announcing it across Facebook, LinkedIn, Threads, and X — each tailored to that platform's format and the free-tier character limits (X 280 chars, Threads 500 chars). Writes a Medium-link + summary + honest positioning line + hashtags, Korean first, each platform saved as its own file separate from the newsletter body. Triggers on "SNS 포스트 써줘", "링크드인/페이스북/쓰레드/엑스 포스트 써줘", "write sns post", "write posts for facebook/linkedin/threads/x" or similar.
---

# Write SNS announcement posts (Facebook, LinkedIn, Threads, X)

## Scope

This writes the short social posts that announce an already-published issue — not the newsletter itself, and not the Medium import/merge (that's `draft-medium-article`) or the repo link update (that's `mark-issue-published`). It produces standalone files per platform; it does not post anything anywhere.

## Prerequisites
- The issue is already published on Medium.
- You have the published Medium URL. If it's not already given or visible earlier in the conversation, ask for it before drafting — don't guess or construct it from the issue number.
- Unless the user already specified which platform(s) they want, ask (default: all four).

## Free-tier constraints (all four accounts are free/standard tier, not paid)

- **X (Twitter)**: **280 characters total**, including the link and hashtags exactly as typed — free tier, no long-post allowance. Count characters yourself before finalizing; don't rely on a link-shortener assumption. This is the tightest constraint — cut to one headline point, not three.
- **Threads**: **500 characters total** on a free account. Enough for a short summary and the link, but not the full 3-bullet LinkedIn treatment.
- **LinkedIn**: no hard limit worth designing around (posts truncate at ~1,300 characters behind "see more", which is fine for the full structure below).
- **Facebook**: no hard limit, but organic reach favors short posts — keep it closer to Threads length than LinkedIn length, conversational tone over bullet-point professional tone.

## Structure by platform

**LinkedIn** (professional, full structure):
1. Short opening line noting the issue was just published.
2. 3 bullet points summarizing the issue's key items.
3. One honest positioning line (see below).
4. Medium link.
5. A handful of hashtags (e.g. `#Tuist #iOS #Xcode #SwiftPM`).

**Facebook** (conversational, condensed):
1. Short opening line.
2. 1–2 sentence summary (prose, not bullets) covering the single most interesting item.
3. Medium link.
4. 1–2 hashtags at most.

**Threads** (casual, tight, ≤500 chars):
1. One-line hook.
2. One sentence naming the top 1–2 items.
3. Medium link.
4. 0–2 hashtags only if they fit.

**X** (tightest, ≤280 chars):
1. One-line hook naming the single most interesting item.
2. Medium link.
3. At most 1 hashtag, only if it fits.

**Honest positioning line** (LinkedIn/Facebook; skip on Threads/X for space): this is a personal project/newsletter, not an official Tuist role — never imply an "ambassador" title, and don't hide that Tuist isn't (yet) used in production at the author's day job, per the project's absolute honesty rule.

## Procedure

1. Read the issue's approved `issues/weekly-tuist-news-N.ko.md` (and `.en.md` if it exists) to pull real content — don't work from memory of the conversation alone if the file has since changed.
2. For each requested platform, draft the Korean version first (Korean is the primary language for this project). Save as `issues/weekly-tuist-news-N-{platform}.ko.md` (`{platform}` = `linkedin`, `facebook`, `threads`, or `x`).
3. For X and Threads, state the actual character count next to the draft so the user can see it's within budget.
4. Ask whether English versions are wanted, unless already requested up front. If yes, translate each (don't re-draft with new framing) and save as `issues/weekly-tuist-news-N-{platform}.en.md` — re-check the char count for X/Threads after translating, since English length can differ from Korean.
5. Show the drafted post(s) to the user. Don't commit automatically — wait for the user to approve or ask you to commit/push, same as any other repo change.

## Don't
- Don't post to any platform — this only produces text files.
- Don't add claims, achievements, or framing not already present in the approved newsletter content.
- Don't assume a paid/premium tier's higher limits (e.g. X Premium's longer posts) — always write to the free-tier limits above unless the user says otherwise.
