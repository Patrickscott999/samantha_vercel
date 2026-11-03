# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Samantha Scott's portfolio site — a fully static, single-file page. There is **no build system, package.json, test suite, or linter**. `index.html` (~3,000 lines) contains all markup, CSS, and JavaScript inline; the only other assets are `board.jpg`, `board-frame.jpg`, `board.mp4`, and `samanthascott-resume.pdf` (served from the repo root).

- **Live deployment:** Vercel (`samanthascott-portfolio.vercel.app`). Netlify credentials were blocked by credits, so `netlify.toml` and `.netlify/` are vestigial — don't rely on them.
- To preview locally, just open `index.html` in a browser or serve the folder statically. No install step.

## Architecture of index.html

Everything lives in one file, in this order:

1. `<head>`: Google Fonts (Fraunces, Newsreader, Italianno, JetBrains Mono, Caveat) and Chart.js 4.4.1 from jsDelivr (`defer`).
2. One large `<style>` block: a CSS-custom-properties design system (`:root` tokens ~lines 16–38) with a "paper card / field notebook" aesthetic — paper ink/rule/edge tokens, fixed grain (`body::before`) and 32px blueprint grid (`body::after`) overlays, Fraunces headings with variable-font settings.
3. Seven `<section>`s tagged with `data-screen-label` ("01 Hero" → "07 Contact"). The Projects section (`#work`, ~line 1950) hosts the interactive demos.
4. One inline `<script>` block (~line 2367) containing all behavior.

Key things to know before editing:

- **Hand-tracking demo** (`enable()` → `loadLandmarker()` ~line 2819): MediaPipe Tasks-Vision 0.10.10 is loaded via dynamic `import()` from jsDelivr at runtime (wasm + `hand_landmarker.task` model from Google's CDN — version bumps must change *both* the module URL and the wasm FilesetResolver URL). Finger-extended classification is a hand-rolled tip-vs-pip y-coordinate heuristic; `CONN` defines the skeleton drawing order.
- **The video/camera demo has a preview-sandbox fallback** (`startFallback(reason)` ~line 2779): `<video>` Range requests fail in some sandboxes (see comment at line 2397), so there's a requestAnimationFrame fallback loop. Don't remove the fallback when touching that code.
- **Chart demo** is self-contained: seeded RNG (`rng(seed)`) → `generate()` time-series with injected anomalies → `stats()`/`render()` into Chart.js → `exportCsv()` builds a client-side CSV download.
- Camera access is user-gesture gated; the script runs after DOM-ready via `ready()`.

## Conventions

- All colors go through the CSS variables in `:root` — extend those tokens rather than hard-coding hex values.
- The hero/section "gutter" design deliberately overflows the content column; `html { overflow-x: clip }` (line 41–48) is load-bearing and comment-documented.
- **Résumés:** two track-specific PDFs live at repo root (`Samantha_Scott_Embedded_Engineer.pdf`, `Samantha_Scott_Software_Engineering.pdf`); both hero and Contact link them as "Résumé — Embedded" / "Résumé — Software" download buttons. Update the PDFs in this repo, not elsewhere; never fabricate résumé content.
- Content is Samantha's real résumé data — don't fabricate projects, employers, or dates.