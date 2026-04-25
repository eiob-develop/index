# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static mini-game collection — 5 vanilla HTML/CSS/JS games, no build step, no dependencies, no package manager. Each game is a single self-contained `.html` file with inline `<style>` and `<script>`. UI language is Korean.

GitHub: https://github.com/eiob-develop/index (public)

## Running / Testing

- Open `index.html` directly in a browser (double-click). No dev server, no compile, no install.
- To verify a change, open the affected game's HTML file and exercise the feature manually.

## Architecture

The site is a flat hub-and-spoke:

- `index.html` is the hub — a card grid linking to each game via relative `href`. Each card carries a `--card-color` CSS custom property used for the accent color.
- Each game (`minesweeper.html`, `2048.html`, `memory.html`, `snake.html`, `rps.html`) is **fully independent**. There is no shared CSS, JS, or asset file. Files do not import each other; they communicate only via navigation.
- Every game page must include a "← 홈" link pointing back to `index.html` (top-left, fixed or in a top bar).
- Persistent state (high scores, RPS streak) uses `localStorage` with per-game keys (`2048-best`, `snake-best`, `rps-stats`).

When adding a new game: create a new `<game>.html` file in the root, then add a `<a href="<game>.html" class="game-card" style="--card-color: #HEX;">` block to `index.html`'s `.games` grid.

## Visual Conventions

All pages share a dark theme palette (defined inline per file, not via shared CSS):

- Background: `#0f172a` → `#1e293b` (linear-gradient 135deg)
- Surface: `#1e293b`, deeper `#283447`
- Text: `#ecf0f1`, muted `#94a3b8`
- Each game has a signature color used for accents/borders/buttons:
  - 지뢰찾기 `#e74c3c` · 2048 `#f1c40f` · 메모리 `#9b59b6` · 스네이크 `#2ecc71` · 가위바위보 `#3498db`

Font stack is `'Segoe UI', Tahoma, sans-serif` everywhere. Layouts use flexbox/grid and are responsive (mobile breakpoint typically `@media (max-width: 600px)`).

## Auto-commit Hook

A Stop hook in `.claude/settings.local.json` runs `.claude/auto-commit.sh` after every Claude turn. The script:

1. Checks `git status --porcelain` for changes
2. If any: `git add -A` + `git commit -m "auto: N개 파일 변경"` + `git push`
3. If none: silent no-op

Implication: **any tracked-file edit is committed and pushed to `origin/main` immediately when the turn ends.** `.claude/` and other entries in `.gitignore` are not tracked, so changes there don't trigger commits. There is no staging step or review window — make sure edits are intentional before ending the turn.

If the hook misbehaves, inspect `.claude/auto-commit.sh` directly (gitignored, lives only locally).

## Git

- Branch: `main` (single branch)
- Local-only git config: `user.name=eiob-develop`, `user.email=eiob@eiob.io`
- Remote: `origin` → `https://github.com/eiob-develop/index.git`
