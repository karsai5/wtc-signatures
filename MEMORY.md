# Project Memory

## Project Overview
Static HTML email-signature generator for Willoughby Theatre Company (WTC).
User fills in name/role/phone/tagline, optionally picks one or more banner
images, then copies the rendered signature (rich or HTML source) into their
email client.

- Repo: `karsai5/wtc-signatures` (remote `origin`, GitHub)
- VCS: **jj (Jujutsu)** colocated with git — never run raw `git` mutations
- Published via **GitHub Pages** from `main` branch / root, served at
  `https://karsai5.github.io/wtc-signatures/`

## File Structure
- `index.html` — the generator app (was `generator.html`, renamed for Pages)
- `signature.html` — static reference example, not linked from anywhere
- `banners/` — banner images committed to the repo
- `wtc-logo-circ-rev-black.png` — the circular logo used in every signature

## Key Architecture / Decisions
- **Single-file app**: all HTML/CSS/JS in `index.html`. No build step.
- **Image URLs are absolute** (`github.com/.../raw=true`) — they must resolve
  for the *email recipient*, not relative to where the generator is hosted.
  Do NOT change these to relative paths.
- **Banner list is fetched at runtime** from
  `https://api.github.com/repos/karsai5/wtc-signatures/contents/banners`.
  Unauthenticated, so 60 req/hr/IP rate limit applies.
- **Signature layout = single `<table>`** with fixed `width="500"` (constant
  `SIG_WIDTH` in the script). Banners are appended as `<tr>` rows with
  `colspan="2"` inside the same table. This was changed from sibling `<div>`s
  because Fastmail's rich-paste was stripping the banner divs.
- Banner `<img>` uses `width="500"` + `style="width:100%; max-width:500px;
  height:auto"` so banners always match signature width.
- Logo `<td>` has an explicit `width` so the fixed-width table doesn't
  stretch the logo column.

## Notes / Gotchas
- `copyRich` previously called `buildSignature()` directly and silently
  dropped banners. It now copies `$("htmlSource").value` (the full rendered
  HTML). If you refactor, keep that in mind.
- If the GitHub API rate-limits, swap `BANNER_API` for a committed
  `banners.json` manifest instead of trying to auth.
- No `.nojekyll` needed — no underscore-prefixed paths.
- To change overall signature/banner width, edit `SIG_WIDTH` constant.

## Current State
- ✅ Banners moved into the table as rows (Fastmail paste fix)
- ✅ `copyRich` fixed to include banners
- ✅ Renamed `generator.html` → `index.html` for GitHub Pages
- ✅ User to enable Pages in repo Settings → Pages, branch=main, dir=/
