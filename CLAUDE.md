# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A self-contained browser Tic Tac Toe game. The entire app — HTML, CSS, and JavaScript — lives in a single file: `index.html`. There is no build step, no dependencies, no package manager, and no test suite.

## Running

- **Local:** open `index.html` in any browser (e.g. `start index.html` on Windows). No server needed.
- **Live:** served via GitHub Pages at https://gdupier.github.io/tic-tac-toe/ directly from the `main` branch root.

## Deployment workflow

GitHub Pages publishes from `main` root, so **every push to `main` auto-deploys** within ~1 minute. The served entry point must remain `index.html` at the repo root — don't rename or move it without updating the Pages config (`gh api repos/gdupier/tic-tac-toe/pages`).

Per the user's standing preference, commit each meaningful change with a clean message and push to `origin` so there's always a revertible saved version.

## Architecture

All game logic is in the `<script>` block of `index.html`. Key pieces:

- **State:** `board` (9-element array, `''`/`'X'`/`'O'`), `current` player, `gameOver`, plus `mode` (`'2p'` | `'cpu'`) and `difficulty` (`'easy'` | `'medium'` | `'hard'`). The human is always `X` and moves first; the CPU is `O`.
- **Move flow:** `play()` handles a click (and gates out clicks during the CPU's turn), delegates board mutation/win-detection/status to `makeMove()`, then in CPU mode schedules `cpuMove()` via `setTimeout`. The board is disabled during the CPU's turn and re-enabled by `enableEmpty()`.
- **AI:** `chooseMove()` branches on difficulty — easy is random, medium is 60% optimal, hard is fully optimal. Optimal play is `bestMove()` backed by a `minimax()` search (depth-aware scoring so it prefers faster wins / slower losses). Hard is intended to be unbeatable; if a human ever beats it, that's a bug.
- **Win detection:** the `wins` array lists all 8 winning index triples, used by both `winningCombo()` (live board) and `evaluate()` (minimax's hypothetical boards).

Note `winningCombo()` and `evaluate()` are near-duplicates over the same `wins` data — one reads the global `board`, the other a passed-in board. UI is rebuilt from scratch on each `init()` (mode/difficulty change or New Game); scores persist in memory across rounds but reset on page reload.
