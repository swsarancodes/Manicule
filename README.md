# Manicule ☞

> An open-source, distraction-free Markdown editor for focused writing.

Manicule edits Markdown **visually** — no preview pane, no compile step, and no AST serialization — while treating the `.md` file as the single source of truth. Untouched bytes are never rewritten.

[![GitHub Release](https://img.shields.io/github/v/release/swsarancodes/Maincule?color=orange&label=Release)](https://github.com/swsarancodes/Maincule/releases/latest)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

```
plain .md on disk  ·  no account  ·  no telemetry  ·  works offline  ·  Git-friendly  ·  MIT licensed
```

---

## Downloads

Grab the latest stable desktop build for macOS, Windows, and Linux from [**GitHub Releases**](https://github.com/swsarancodes/Maincule/releases/latest):

| Platform | Format | Architecture | Download |
|---|---|---|---|
| **macOS (Universal)** | `.dmg` | **Apple Silicon & Intel** *(works on all Macs)* | [**Download Universal DMG**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_universal.dmg) |
| **macOS (Apple Silicon)** | `.dmg` | Apple Silicon (M-series) | [**Download DMG (ARM64)**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_aarch64.dmg) |
| **macOS (Intel)** | `.dmg` | Intel x86_64 | [**Download DMG (x64)**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_x64.dmg) |
| **Windows** | `.exe` | 64-bit installer | [**Download Setup (.exe)**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_x64-setup.exe) |
| **Windows** | `.msi` | 64-bit MSI package | [**Download Package (.msi)**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_x64_en-US.msi) |
| **Linux** | `.AppImage` | x86_64 portable | [**Download AppImage**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_amd64.AppImage) |
| **Linux** | `.deb` | Ubuntu / Debian package | [**Download .deb**](https://github.com/swsarancodes/Maincule/releases/download/v0.6.2/Manicule_0.6.2_amd64.deb) |

> [!TIP]
> **macOS first-launch note (Gatekeeper)**  
> Manicule is unsigned (no paid Apple Developer certificate), so Gatekeeper may warn on first open.  
> - **Option 1**: Right-click (or Control-click) `Manicule.app` in `/Applications`, choose **Open**, then click **Open**.  
> - **Option 2**: Clear quarantine in Terminal:
>   ```bash
>   xattr -d com.apple.quarantine /Applications/Manicule.app
>   ```

---

## Why this editor?

Typical Markdown editors force a compromise:

| Category | Typical Behavior | The Trade-off |
|---|---|---|
| **Split-pane** | Source text on the left, HTML preview on the right | Split attention: you read in one pane and write in another, with constant visual context switching. |
| **WYSIWYG** | Parses Markdown into a rich-text tree (ProseMirror / Slate / Lexical) and re-serializes on save | **Lossy round-trip:** rewrites list markers, collapses custom whitespace, reformats front matter, and can mangle raw HTML. |

Manicule takes another path: **source-of-truth editing with live decorations**.

The plain Markdown text in CodeMirror 6 is the only document model. We conceal syntax tokens visually and apply typography directly over the source text. You get visual WYSIWYG ergonomics while keeping 100% byte fidelity with disk and Git.

> **Correctness invariant:** If Manicule ever rewrites a line or marker you did not edit, that is a P0 defect.

---

## Core Principles

1. **The file is the truth:** Plain `.md` on disk. No proprietary database and no lock-in.
2. **Lossless or nothing:** Round-tripping any document yields byte-identical output, including line endings, BOM, formatting characters, and whitespace.
3. **Local-first and offline-always:** No account, no required cloud sync, and zero telemetry.
4. **Distraction-free:** Minimal chrome, a typography-focused measure (`78ch`), and immersive writing modes.
5. **Fast at scale:** Viewport-scoped decorations and incremental Lezer parsing.

---

## Features

- **Hybrid Visual Mode (`Cmd+1` / `Ctrl+1`)**: Live concealment of syntax tokens (`**bold**`, `*italic*`, `~~strike~~`, `` `code` ``, `[links](url)`, `# headings`) with interactive Notion-style tables, bullet dots (`•`), checkboxes, and Mermaid diagrams.
- **Raw Source Mode (`Cmd+2` / `Ctrl+2`)**: Syntax-highlighted source editor with zero concealment.
- **Split Mode (`Cmd+3` / `Ctrl+3`)**: Side-by-side visual and source views with synchronized document state.
- **Floating Selection Toolbar**: Selection bubble for bold, italic, strikethrough, headings, and lists.
- **Slash Commands (`/`)**: Type `/` to insert tables, diagrams, callouts, checklists, and code cards.
- **Command Palette (`Cmd+K` / `Cmd+P` / `Ctrl+K` / `Ctrl+P`)**: Quick search, note switching, and command execution.
- **Writing Analytics**: Live word count, character count, and reading-time estimate in the status bar.
- **Adaptive Themes**: Light and dark modes driven by CSS custom properties.
- **Atomic Saves**: Rust atomic writes (`tempfile` → `fsync` → `rename`) with SHA-256 conflict detection.

---

## Architecture & Stack

```
+--------------------------------------------------------------+
| L1  EDITOR CORE          CodeMirror 6 + Lezer parser         |
|     buffer · caret · decorations · undo/redo · atomicRanges  |
+--------------------------------------------------------------+
| L2  MARKDOWN PIPELINE    Lezer md grammar + GFM extensions   |
|     incremental parse · decoration mapping · node classifiers|
+--------------------------------------------------------------+
| L3  APP UI               React 19 + TypeScript + Zustand     |
|     sidebar · tabs · command palette · status bar · themes   |
+--------------------------------------------------------------+
| L4  SHELL                Tauri v2 (Rust)                     |
|     atomic FS · file watcher · native dialogs · packaging    |
+--------------------------------------------------------------+
```

---

## Quickstart

### Prerequisites

- **Bun** (recommended) or Node.js 20+
- **Rust stable** toolchain with `cargo`
- **Xcode Command Line Tools** on macOS (for native compilation)

### Running Locally

```bash
# 1. Clone
git clone https://github.com/swsarancodes/Maincule.git
cd Maincule

# 2. Install dependencies
bun install

# 3. Start the Tauri desktop app
bun run tauri dev
```

### Running Tests

```bash
# Lossless round-trip and fidelity property tests
bun test
```

---

## Contributing

Contributions are welcome. Please make sure that:
1. Review the relevant spec in `docs/` before making architectural changes.
2. Keep round-trip fidelity tests passing (`bun test`).
3. Changes to `src/core/` stay free of React imports.

See [CONTRIBUTING.md](docs/CONTRIBUTING.md) for the full guide.

---

## License

Released under the [MIT License](LICENSE). See the license file for the full text.
