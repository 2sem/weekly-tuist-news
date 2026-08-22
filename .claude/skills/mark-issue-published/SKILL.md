---
name: mark-issue-published
description: Use when the user says an issue has been published on Medium and gives (or you already have) the article URL — updates docs/index.md to point at the Medium link, drops the now-stale docs/issues/N draft preview pages, and updates the local issues/weekly-tuist-news-N.ko.md/.en.md archive headers to note the published URL. Triggers on "published #N. update repo with link: <url>", "N호 발행했어, 링크 업데이트", "repo에 미디엄 링크 반영해줘" or similar.
---

# Mark an issue published, sync the repo link

## Scope

This is the small bookkeeping step that runs *after* a human has already clicked Publish on Medium and hands you the live URL. It does **not**:
- fetch or re-read the published Medium article — trust the local `issues/weekly-tuist-news-N.*.md` content as the archive; don't spend a WebFetch/browser round-trip reading the live page unless the user explicitly asks for a full content sync (see "Full content sync" below)
- write the SNS/LinkedIn post — that's a separate ask
- touch Medium in any way (no browser automation here)

## Prerequisites
- `docs/issues/N.ko.md` and `docs/issues/N.en.md` currently exist as draft preview pages (created by the format/deploy step before Medium import).

## Procedure

0. **Get the article URL**: if the user's request doesn't include the published Medium URL and it isn't already visible earlier in the conversation, ask for it before doing anything else — don't guess or construct it from the issue number.

1. **Normalize the URL**: strip tracking query params (e.g. `?sharedUserId=...`) so it matches the plain-URL style already used for other issues in `docs/index.md`.

2. **Update `docs/index.md`**: replace issue N's row — swap the `[한국어](issues/N.ko.html) / [English](issues/N.en.html)` dual-link and "Draft/Approved — ..." status for a single `[Weekly Tuist News #N](MEDIUM_URL)` link with status `Published on Medium`, matching the format of already-published rows.

3. **Drop the draft preview pages**: `git rm docs/issues/N.ko.md docs/issues/N.en.md`. These existed only so Medium's importer had a public URL to pull from; once published they're stale (and would otherwise keep advertising a "pending" status on the Pages site).

4. **Update the local archive headers** in `issues/weekly-tuist-news-N.ko.md` and `.en.md`: replace the pre-publish note block (whatever it says — "승인됨, 미발행" / "draft, translated from approved Korean" / etc.) with:
   - Korean: `발행: Medium, Lee young-jun (toyboy2) — MEDIUM_URL` then a note that this is a local archive of the published article.
   - English: `Published: Medium, by Lee young-jun (toyboy2) — MEDIUM_URL` then the equivalent note.
   - Since this project publishes one bilingual Medium article (English section + Korean section in a single page, per `draft-medium-article`), **both** the `.ko.md` and `.en.md` archives point at the *same* URL — keep them as two separate per-language local files (this mirrors how issues #1 and #2 were archived; don't collapse them into one merged file unless the user asks).
   - Don't touch the body content below the header/note block — leave it as whatever was last approved locally.

5. **Commit and ask before pushing**: stage the four changed files (`docs/index.md`, the two removed `docs/issues/N.*.md`, the two updated `issues/weekly-tuist-news-N.*.md`), commit as `Mark issue #N published, link Medium, drop draft preview pages`, then ask the user for explicit confirmation before `git push` (pushing to `main` updates the public GitHub Pages site).

## Full content sync (only if asked)

If the user separately wants the local archive's *body* brought in line with what actually shipped (title tweaks, wording fixed during Medium's editor, an added closing block, etc. — see the issue #2 precedent commit `15bfbd5`), that's a bigger step: read the live article and reconcile differences. Don't do this automatically — only when explicitly requested, since it means fetching the published page.
