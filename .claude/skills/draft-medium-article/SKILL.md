---
name: draft-medium-article
description: Use when an issue's ko/en pages are live on GitHub Pages and you need to get them onto Medium as drafts — imports both language pages via Medium's "Import a story", merges them into one combined draft (English full, then Korean full), and leaves the two throwaway single-language imports for the user to delete. Triggers on "미디엄 초안 가져와줘", "미디엄에 임포트 해줘", "N호 미디엄 드래프트 만들어줘" or similar.
---

# Import + merge a Medium draft

## Prerequisites
- The issue's `docs/issues/{N}.ko.md` and `docs/issues/{N}.en.md` are already deployed to GitHub Pages and publicly reachable (`https://2sem.github.io/weekly-tuist-news/issues/{N}.ko.html`, `.../{N}.en.html`). If not, deploy first — this skill doesn't do that.
- Uses the Chrome browser tools (`mcp__claude-in-chrome__*`). If multiple browsers are connected, ask the user which one first (`select_browser`).
- Must be logged into Medium already (check `medium.com/me/stories`).

## Procedure

1. **Confirm the URLs**: build the two public URLs for issue N (ko/en). Verify both return 200 (`curl -I`).

2. **Import each language** (ko, en, order doesn't matter):
   - Go to `medium.com/p/import`
   - Click the URL field → paste → click **Import**
   - Wait ~2-3s for the "Imported the story" tutorial overlay, then close it via the X in the top-right (it's Medium's own onboarding UI, not real content — don't mistake it for the article)
   - Note the resulting draft's `medium.com/p/<id>/edit` URL (you'll end up with two: a ko id and an en id)
   - **Caveat**: the URL shown right after import can later change to a different id once autosave settles. Before merging, re-check the actual final ids on `medium.com/me/stories` → Drafts (titles look identical, so disambiguate by word count against the known ko/en source word counts).
   - Imported titles have been observed to drop a trailing `#N` (e.g. "Weekly Tuist News #2" → "Weekly Tuist News"). Leave the title for the user to review/fix after merging — don't try to silently correct it yourself.

3. **Create the merged draft** (English in full, then a divider, then Korean in full — matches the `tuist-newsletter-format` skill's language-layout rule):
   - On the English draft tab, click anywhere in the body (not the title) → `cmd+a` (selects title + body together — verify the highlight in a screenshot) → `cmd+c`
   - Open `medium.com/new-story` in a fresh tab → click the Title field → `cmd+v` (title and body both land in one paste)
   - `cmd+End` to jump to the end → type `---` then Enter (Medium auto-converts this to a horizontal-rule divider)
   - Switch to the Korean draft tab → click the body → `cmd+a` → `cmd+c`
   - Switch back to the merged tab → `cmd+v`
   - Scroll to the top (`cmd+Home`) and screenshot to confirm the structure: title → full English → divider → full Korean

4. **Cleaning up the two temp imports is the user's job, not yours**: once merged, the two single-language drafts are no longer needed, but **do not delete them yourself** — permanent deletion is a prohibited action (see memory: `medium_story_deletion`). Medium has no direct delete URL, only the `···` menu's "Delete story" button (a JS action, no href). Point the user at the exact titles/ids/word counts to delete, and give them the manual path: `medium.com/me/stories` → `···` → Delete story → confirm. Never suggest deleting the merged draft (the one with a word count near ko+en combined).

5. **Finish with the merged draft open in the browser**: leave (or re-navigate) the browser tab on the merged draft's `medium.com/p/<id>/edit` URL and report that URL back to the user.

## Don't
- **Never click Publish.** Both the imports and the merge stay as drafts (CLAUDE.md absolute rule: a human always presses the final Publish).
- **Never click "Delete story" (or confirm its dialog).** Only tell the user where it is and which drafts to target.
- The import modal's text ("Imported the story", the 3-step tutorial blurb) isn't real article content — don't confuse it with the actual draft when reading screenshots.
