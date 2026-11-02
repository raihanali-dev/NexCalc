# Scientific Calculator Pro

Precision in Every Calculation.

A production-quality scientific calculator web application. All evaluation happens in the browser with a restricted [mathjs](https://mathjs.org/) engine — no `eval()`, no remote calculation APIs, and no arbitrary JavaScript execution.

## Features

- Arithmetic with standard operator precedence and nested parentheses
- Trigonometry with **DEG** and **RAD** modes (including inverse and hyperbolic functions)
- Logarithms (`log` = base 10, `ln`, `log2`, `log(x, base)`), exponentials, powers, and roots
- Factorials (non-negative integers 0–170), permutations `nPr`, combinations `nCr`
- Constants π, e, τ, and φ
- Percent (`200 × 10%` = 20, `200 + 10%` = 220) and remainder via `mod(a, b)`
- Memory: MC, MR, M+, M−, MS
- Calculation history with reuse, copy, delete, and local persistence
- Keyboard input, editable expression field, copy/paste
- Light / dark / system themes and display-formatting preferences
- Tools: percentage calculator, descriptive statistics, linear `ax + b = c` solver, unit converter

## Technology stack

- React 19, TypeScript, Vite
- Tailwind CSS 4 (with a dedicated design system on top)
- mathjs (restricted function/symbol allowlist)
- Lucide React icons
- Vitest and React Testing Library

## Installation

```bash
npm install
```

## Commands

```bash
npm run dev       # development server
npm run build     # typecheck and production build
npm run preview   # serve the production build
npm test          # run unit and interaction tests
npm run lint      # oxlint
```

Open the URL printed by `npm run dev` (typically `http://localhost:5173`).

## Project structure

```
src/
  components/     UI: calculator, history, settings, tools
  hooks/          Calculator state, keyboard, preferences, history
  lib/calculator/ Expression engine (normalize, evaluate, format)
  lib/tools/      Statistics, units, linear solver, percentage
  types/          Shared types
  utils/          Local storage and clipboard helpers
```

## Mathematical engine

1. Display symbols (`×`, `÷`, `π`, `²`, …) are normalized to evaluator tokens.
2. Identifiers are checked against an allowlist (`sin`, `log`, `pi`, …). Unsupported names such as `eval` are rejected.
3. Parentheses, length, and parse-tree size are bounded.
4. mathjs parses and compiles the expression. Trigonometric wrappers convert DEG ↔ RAD in one place (`src/lib/calculator/angleMode.ts` and `mathInstance.ts`).
5. Complex, non-finite, and domain errors become plain-language messages. The original expression is kept so it can be edited.

Internally, values stay as IEEE 754 numbers. Display rounding, scientific notation, thousands separators, and optional fraction hints never replace the stored result.

### DEG / RAD

- **DEG**: `sin(30) = 0.5`, `asin(0.5) = 30`
- **RAD**: `sin(pi / 2) = 1`, `atan(1) = π / 4`

Hyperbolic functions take dimensionless numeric arguments (no angle conversion).

### Repeated equals

After a binary expression such as `10+5` evaluates to 15, pressing `=` again computes `15+5`, and so on.

### Percentage vs remainder

- `%` is **percent**, not modulo.
- Remainder is **mod(a, b)**.

## Intentionally not supported

- Complex numbers (for example `sqrt(-1)` is a domain error)
- Non-integer factorials / gamma
- Symbolic algebra or nonlinear equation solving
- Implicit “of” language (`50% of 800`); use `800 × 50%`
- Sound feedback
- Progressive Web App / service worker
- Sending expressions to any server

Floating-point example: `0.1 + 0.2` is stored as a binary approximation close to `0.3`. The display may show `0.3`.

## Accessibility

Controls have names or `aria-label`s, theme and angle mode are not color-only, the expression field is labeled, results and errors use live regions, and `prefers-reduced-motion` (plus a setting) disables button motion.

## Browsers

Current Chromium, Firefox, and Safari releases. Clipboard actions require a secure context (HTTPS or localhost) and permission.

## Deployment

This is a static Vite app. After `npm run build`, deploy the `dist/` folder to GitHub Pages, Netlify, Cloudflare Pages, or any static host.

```bash
npm run build
```

## Tests

Engine tests cover arithmetic, scientific functions, trigonometry in both angle modes, percentages, and error cases. UI tests cover keypad entry, clear/backspace, DEG/RAD, memory, history, and theme switching.
