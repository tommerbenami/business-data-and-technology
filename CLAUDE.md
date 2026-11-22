# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this folder is

Coursework folder for a second-year university course, "Business Data and Technology". It is not a git repository. There is no build system, package manager, tests or linters. The only code is one static HTML file (see below), and `node` is not installed.

## Current project: small business ↔ investor matching platform

- The product spec is a Hebrew Word document, kept outside this folder:
  `C:\Users\ASUS\Downloads\אפיון פלטפורמת חיבור בין בעלי עסקים קטנים למשקיעים _סופי_.docx`
  To read it, extract `word/document.xml` from the .docx (zip) and strip the tags.
- The model: private investors give a small Jerusalem business a fixed amount, typically 20,000–25,000 ₪. In return they get a fixed % of the business's annual *contractual profit* for up to 10 years, with no ownership. The spec covers dating-app-style swiping, matching on amount and % ranges, a negotiation window, a single contract template with required fields, money held by a payment provider or licensed trustee, a 0.8% fee paid by the business, annual reporting and a 1–10 profile-completeness score.
- Deliverable (built): a clickable MVP prototype, one self-contained Hebrew RTL HTML page with demo data and no backend. Source: `mvp-prototype/index.html`. Published (private) as a claude.ai Artifact at https://claude.ai/artifact/NdPUVTiYKMDWpLAm3XdwY1. To update it, edit the file and republish with the Artifact tool using that URL. Plans: `C:\Users\ASUS\.claude\plans\c-users-asus-downloads-graceful-fog.md` and `...floating-robin.md`. The script has not been syntax-checked or click-tested yet.
- The Artifact tool wraps the file in its own skeleton, so the file has no `<!doctype>`, `<html>` or `<body>` tags. Opening it directly in the built-in browser pane does not work (it refuses local files); test via the published artifact instead.

## Prototype architecture (`mvp-prototype/index.html`)

- One global state object `S`, persisted to localStorage under `mvp-demo-v1` (try/catch), and a single `render()` that rebuilds the phone and side panel from `S` as HTML strings. Inputs carry `data-b="path"` and a delegated `input` handler writes to `S` and re-renders. `render()` restores focus, selection and scroll, so every input needs a stable `id`. Buttons use `data-act` and are dispatched through the `ACT` map.
- The presenter switches between business-owner and investor roles with a toggle. Everything lives in `S.deals`: one deal per business/investor pair, with status `interested → negotiating → contract → signed → closed` (or `rejected`/`ended`). The "me" business has id `me` and the "me" investor has id `meInv`; other businesses (`BIZ`) and investors (`SEED_INV`) are seeded demo data. Actions by the counterparty are simulated with dashed "דמו:" buttons.
- When the business owner approves a match (`ACT.approve`, or `ACT.demoApprove` in the investor view), `celebrate(d,…)` calls `showMatch(d,onGo)`. That shows an "It's a Match!" dialog over the phone (`.match`: initials of both sides, CSS confetti), once per deal via `d.celebrated`, with no motion under `prefers-reduced-motion`. It must be called after the action's `render()`, because `render()` replaces `#phone`'s content. Plan: `plans/its-a-match.md`. This feature lives on branch `feature/its-a-match`, not yet merged to `main`.
- Spec rules encoded as constants and checks: `FEE` 0.8%, `MAXTERM` 10 years, `AUDIT_GAP` 5%, `DROP` (profit drop that requires a written explanation), contractual profit = pre-tax profit plus adjustments (a)–(d), signing blocked on empty required fields or an unticked risk warning, empty platform stats shown as "אין נתונים", deal closed only when signed and deposited.

## Conventions

- All user-facing content is in Hebrew with right-to-left layout (`dir="rtl"`).
- Environment: Windows 11 with PowerShell 5.1, so `&&` is not available. Use `;` or `if ($?) { ... }` to chain commands.
- The Stitch MCP server (Google's UI design generator) is configured at user scope and can be used for UI mockups. Stitch project "פלטפורמת חיבור עסקים קטנים ומשקיעים" (id 6732068022794653908) exists, but its first screen generation timed out and it has no screens yet.
