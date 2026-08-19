# typst-mace

A Typst template emulating the style of the MACE Adventurer's Codex.

- **Body font:** Study, falling back to EB Garamond
- **Display/header font:** Goudy Mediaeval,
  falling back to Manufacturing Consent
- **Page:** A5, 0.5in margins, 0.65in inner gutter, centered folio in the footer

## Fonts

Study and Goudy Mediaeval are not redistributable, so they are *not* in this
repo — install them locally if you have them. The fallbacks (EB Garamond and
Manufacturing Consent, both SIL OFL) are vendored in `fonts/` with their
licenses, so the template renders a faithful approximation anywhere.

Typst does not auto-load fonts from package directories, so point builds at
`fonts/` with either:

```sh
typst compile --font-path /path/to/typst-mace/fonts main.typ
```

Or set it once per shell session:

```sh
# macOS / Linux (bash, zsh)
export TYPST_FONT_PATH=/path/to/typst-mace/fonts

# fish
set -gx TYPST_FONT_PATH /path/to/typst-mace/fonts

# Windows (PowerShell)
$env:TYPST_FONT_PATH = "C:\path\to\typst-mace\fonts"
```

With the preferred fonts installed they win automatically (they are listed
first in the font stack), the flag just makes the fallbacks available and
silences "unknown font family" warnings. Without any of this, builds still
succeed on machines with the preferred fonts — you may just see harmless
warnings about the missing fallback families.

## Installing as a local package

Link this directory into Typst's local package store, matching the version
in `typst.toml`. The store lives in a different place per OS:

| OS      | Local package store                                            |
| ------- | -------------------------------------------------------------- |
| macOS   | `~/Library/Application Support/typst/packages/local`           |
| Linux   | `$XDG_DATA_HOME/typst/packages/local` (usually `~/.local/share/typst/packages/local`) |
| Windows | `%APPDATA%\typst\packages\local`                               |

**macOS:**

```sh
mkdir -p "$HOME/Library/Application Support/typst/packages/local/mace"
ln -sfn /path/to/typst-mace "$HOME/Library/Application Support/typst/packages/local/mace/0.1.0"
```

**Linux:**

```sh
mkdir -p "${XDG_DATA_HOME:-$HOME/.local/share}/typst/packages/local/mace"
ln -sfn /path/to/typst-mace "${XDG_DATA_HOME:-$HOME/.local/share}/typst/packages/local/mace/0.1.0"
```

**Windows:**

```powershell
New-Item -ItemType Directory -Force "$env:APPDATA\typst\packages\local\mace" | Out-Null
New-Item -ItemType Junction -Path "$env:APPDATA\typst\packages\local\mace\0.1.0" -Target "C:\path\to\typst-mace"
```

(On any OS, copying the directory there works too.)

Books then import it with `#import "@local/mace:0.1.0": ...` instead of a
relative path. While the symlink points at this working copy, template edits
are live under `0.1.0`; to freeze a version (so released books can't reflow),
replace the symlink with a real copy and continue development under a bumped
version number (`0.2.0`), updating each book's import when you migrate it.

## Usage

```typst
#import "@local/mace:0.1.0": codex, codex-table, title-page, table-of-contents

#show: codex.with(title: "My Codex", author: "Me")

#title-page(
  title: [MACE],
  tagline: [Medieval Adventure Campaign Engine],
  subtitle: [Adventurer's Codex],
  art: image("art/cover.png", width: 70%),   // optional
  blurb: [Rules additions & modifications],  // rendered in caps
  version: [version 08-19-2026],
  license: [© 2026 ... CC BY-SA 4.0],
)

#table-of-contents()

= Chapter Heading        // Goudy Mediaeval, 19pt
== Section Heading       // bold, letterspaced
=== Sub-heading          // bold italic

#codex-table(
  columns: (1fr, 1fr),
  align: (left, center),                     // optional, defaults to center
  header: ([Ability Score Rolled], [Modifier]),
  [3], [--2],
  [4--6], [--1],
)
```

`codex-table` produces the codex's rules-only table style: 1pt rule above a
small-caps bold header, thin rule beneath it, zebra-striped rows, and a
closing rule. Headers repeat automatically when a table breaks across pages.

Bullet and numbered lists, footnotes (small italic, no separator), and
images (auto-centered) are styled by the `codex` wrapper.

Build the sample with:

```sh
typst compile example.typ
```

## License

The template code is licensed under the **LGPL-3.0-or-later** (`LICENSE` /
`COPYING.LESSER`, with the underlying GPL in `COPYING`): if you modify and
distribute the template itself, share those changes under the same terms. 

Documents merely *typeset with* this template are your own work and may be licensed however you like.

The fonts in `fonts/` are under the **SIL Open Font License** (license texts
alongside them); book content is licensed separately, per book, usually **CC BY-SA 4.0**.
