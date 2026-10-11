# font-changer

**Font changer for Termux with Nerd Fonts.**

Give your terminal a fresh look with a friendly fullscreen picker. Browse
the fonts you already have, preview each one live against your real shell
prompt, and keep the one you love — with automatic backups, so you can
always go back.

The picker styles itself with your Termux colors and renders your actual
prompt in every candidate font, so what you see is what you get.

## Install

You need [Go](https://go.dev/dl/) 1.26 or newer, then run:

```bash
go install github.com/GeneralKaos666/font-changer/cmd/font-changer@latest
```

This puts a `font-changer` command on your system. If your shell
can't find it afterwards, add Go's install folder to your `PATH`, or
install straight into Termux's own folder instead:

```bash
GOBIN=$PREFIX/bin go install github.com/GeneralKaos666/font-changer/cmd/font-changer@latest
```

No Go toolchain? Grab the prebuilt arm64 binary from the
[latest release](https://github.com/GeneralKaos666/font-changer/releases/latest):

```bash
curl -L -o font-changer \
  https://github.com/GeneralKaos666/font-changer/releases/latest/download/font-changer-android-arm64
install -Dm755 font-changer "$PREFIX/bin/font-changer"
```

Want to poke around safely first? Point the tool at a throwaway folder
so your real setup stays untouched:

```bash
TERMUX_HOME=$PREFIX/tmp/fc-go-demo font-changer --list
```

### Upgrading from older names

This tool has been renamed over time. Up to v0.1.0 it installed a
`termux-fonts` command from a `font-changer-termux` module path, and it
was later called `nerdfont-changer`. The current name is `font-changer`.
If you have an old command installed, remove it and reinstall:

```bash
rm -f "$(command -v termux-fonts)"     # usually in $PREFIX/bin or ~/go/bin
GOBIN=$PREFIX/bin go install github.com/GeneralKaos666/font-changer/cmd/font-changer@latest
```

## Usage

Open the picker:

```bash
font-changer
```

The top half previews the highlighted font: a sample alphabet, symbol
coverage, facts about the font (glyph count and version), your actual
shell prompt, and which terminal slot it would fill. The bottom half is
your font collection — fonts already in use carry a `● slot` badge.
The list is ready for arrow keys right away; press `Tab` to jump to the
search box. On a short terminal the preview folds away and the list takes
the whole screen; a window smaller than 44 columns by 8 rows gets a
"terminal too small" notice instead of a squashed frame.

Prefer the command line? These work too (great for scripts):

```bash
font-changer --list
font-changer --apply "JetBrainsMono.ttf" --slot regular
```

Termux has four font slots: `regular`, `bold`, `italic`, and
`bold-italic` (`regular` is the default). `--apply` finds the font by
name (exact match first, then case-insensitive), checks that the file
is a healthy font, saves a backup of whatever was there before,
installs the new font, and tells Termux to reload.

### Keys

| Key       | What it does                        |
| --------- | ----------------------------------- |
| Space/`p` | Try the highlighted font, live      |
| Enter     | Keep the font you're trying         |
| `s`       | Switch between the four font slots  |
| `i`       | Add one of your own font files      |
| `d`       | Download a Nerd Font                |
| Esc       | Go back / undo an untried preview   |
| `q`, Ctrl+C | Quit (undoes a preview you didn't keep) |
| Ctrl+Z    | Suspend to the shell; run `fg` to return |
| Tab       | Jump to the search box              |

The footer always shows the keys that work on the screen you're on, so
you don't have to remember the full list.

If your terminal renders box-drawing characters as garbage, start the
picker with `font-changer --ascii` (or set `FONT_CHANGER_ASCII=1`)
for plain-ASCII borders and markers.

Just looking around changes nothing — a font is only installed when you
preview it (Space/`p`) or keep it (Enter).

## Matching your style

When you open the picker, it reads your Termux color theme
(`~/.termux/colors.properties`) and styles itself to match. Set a theme
with [termux-colors](https://github.com/GeneralKaos666/color-changer-termux)
and this picker follows along. No theme file? It falls back to calm
built-in colors.

## Backups — you can't break anything

The first time each session that a font would replace something, the old
file is copied to `~/.termux/backups/` with the date and time in its
name (for example `font-2026-10-06-120500.ttf`). Trying more fonts reuses
that same backup instead of piling up copies. And if you quit while
trying a font you never kept, the original is put back automatically.

## First run

If your font collection is empty but Termux already has a font active,
the tool copies it into the collection as `Current.ttf` on startup, so
the picker is never blank. This happens for both the visual picker and
the command-line flags.

## A note on the old Python version

This tool started life as a Python program. That version is retired and
no longer maintained — this Go rewrite replaces it completely. Curious
archaeologists can still find it in this repo's history from before the
Go commits (`git log --all -- src/termux_fonts/`).
