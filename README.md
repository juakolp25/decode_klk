# decode_klk

> A single-file, zero-dependency intelligent decoding tool with auto-detection across 6 encoding formats.

```
 _   _ ___ ____
| | | |_ _|  _ \
| | | || || | | |
| |_| || || |_| |
 \___/|___|____/

Universal Intelligent Decoder v1.0
```

---

## Overview

UID is a brutalist-minimal single-page application (`index.html`) that automatically detects and decodes encoded text in real time — no button presses, no server, no dependencies. Paste your encoded input and the engine identifies the format and outputs the decoded result instantly.

---

## Features

- **Auto-detection engine** — analyses input and selects the correct format automatically
- **6 supported formats** — Brainfuck, Binary, Hexadecimal, Base64, Morse, ROT-13
- **Real-time decoding** — driven by the `input` event, updates on every keystroke
- **Format indicator** — `[DETECTADO: FORMAT]` label always visible in the output panel
- **Confidence scoring** — ALTA / MEDIA / BAJA based on format-specific heuristics
- **Zero dependencies** — pure HTML + CSS + vanilla JS, one file, works offline
- **Brainfuck interpreter** — full BF engine with bracket map pre-computation and 200k step limit
- **Responsive** — two-column desktop layout collapses to single-column on mobile
- **Copy to clipboard** — one-click copy of decoded output

---

## Supported Formats

| Format | Detection Method | Notes |
|---|---|---|
| **Brainfuck** | Presence of `[ ] > < + - . ,` | Full interpreter, 200k step safety limit |
| **Binary** | Only `0`, `1` and whitespace | Groups into 8-bit chunks, validates ASCII output |
| **Hexadecimal** | `0–9`, `A–F` pairs | Accepts space, `:` or raw separators |
| **Base64** | Regex + length multiple of 4 | Uses native `atob()`, validates printable output |
| **Morse** | Only `.`, `-`, `/` and spaces | 46-symbol table, `/` or double-space as word separator |
| **ROT-13** | Plain ASCII text heuristic | Activates only when rotated output matches common English words |

### Detection Priority

The engine tests formats in this exact order, stopping at the first successful decode:

```
1. Brainfuck  →  2. Binary  →  3. Hex  →  4. Base64  →  5. Morse  →  6. ROT-13
```

---

## Usage

No installation required.

```bash
# Clone the repo
git clone https://github.com/juakolp25/decode_klk.git

# Open the file in any browser
open index.html
```

Or simply download `index.html` and open it locally — it runs entirely in the browser.

---

## Input Examples

```
Binary       →   01001000 01100101 01101100 01101100 01101111
Hexadecimal  →   48 65 6C 6C 6F
Base64       →   SGVsbG8gV29ybGQ=
Morse        →   .... . .-.. .-.. --- / .-- --- .-. .-.. -..
ROT-13       →   Uryyb Jbeyq
Brainfuck    →   ++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++.>>.<-.<.+++.------.--------.>>+.>++.
```

---

## Technical Design

### Architecture

The entire application is a single HTML file structured in three layers:

```
decoder.html
├── <style>   — CSS custom properties, brutalist grid layout, animations
├── <body>    — Two-panel layout (input / output) + format bar + metadata strip
└── <script>  — Auto-detection engine + 6 decoders + reactive UI logic
```

### Brainfuck Engine

The BF interpreter pre-computes a bracket lookup map at parse time, reducing `[` / `]` jumps from O(n) linear scans to O(1) lookups. A step counter hard-stops execution at 200,000 cycles to prevent infinite loops.

```js
// Bracket map pre-computation
for (let i = 0; i < bf.length; i++) {
  if (bf[i] === '[') stack.push(i);
  else if (bf[i] === ']') {
    const open = stack.pop();
    bracketMap[open] = i;
    bracketMap[i] = open;
  }
}
```

### ROT-13 Heuristic

Since ROT-13 is always syntactically valid plain text, a naive detector would trigger on any string. UID scores the post-rotation output against a list of ~35 high-frequency English words. Decoding is only shown if at least one match is found, and confidence is graded by match count.

### Confidence Levels

| Level | Meaning |
|---|---|
| `ALTA` | Format signature unambiguous, output fully printable |
| `MEDIA` | Format matched but output contains minor anomalies |
| `BAJA` | Weak signal — possible false positive |

---

## Design System

The UI follows a **Brutalist Minimal** aesthetic:

- **Palette:** `#000000` / `#FFFFFF` — no grays, no gradients
- **Borders:** `2px solid #000` — no border-radius anywhere
- **Typography:** `'Courier New', Courier, Consolas, monospace` — system monospace stack
- **Motion:** CSS `@keyframes flash-in` on each new decode; blinking cursor on idle state
- **Layout:** CSS Grid two-column split at desktop, single column below 700px

---

## Browser Support

Works in any modern browser with ES6+ support. No build step, no bundler, no npm.

| Chrome | Firefox | Safari | Edge |
|---|---|---|---|
| ✓ 80+ | ✓ 75+ | ✓ 14+ | ✓ 80+ |

---

## License

MIT — do whatever you want with it.

---

*Built with pure HTML, CSS, and vanilla JavaScript. No frameworks were harmed.*
