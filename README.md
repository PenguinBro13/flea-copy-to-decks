# Copy to Decks — a Flea plugin

A [Flea](https://github.com/thisisgm/flea) menu action: select files, right-click,
**Copy to Decks**, and they are copied into your Decks folder (`~/Decks` by default).

Built for the [Flea plugin protocol](https://github.com/thisisgm/flea/blob/main/PLUGINS.md)
(`menu-action`, schema version 1). No build step, no dependencies beyond Python 3.

## Install

```bash
flea plugin add https://github.com/PenguinBro13/flea-copy-to-decks.git
flea plugin enable deck.inbox
```

Restart Flea if it is already running. The row appears in the **file menu** while
at least one non-folder file is selected.

`~/Decks` is not created for you:

```bash
mkdir -p ~/Decks
```

## Behavior

- Accepts `.docx`, `.txt`, `.md`, `.markdown`, `.pptx`, `.potx`. Any other type is
  refused with a notice rather than silently skipped.
- Copies by default, preserving timestamps. Files already in the destination are
  not duplicated.
- Never overwrites: a repeated name becomes `brief-2.docx`, then `brief-3.docx`.
- Hidden for folders, so it cannot fire on a directory selection.
- Reports per-file progress and finishes with `2 files copied.`

## Settings

**Settings → Plugins → Deck Inbox**

| Key | Default | Meaning |
|---|---|---|
| Destination folder | `~/Decks` | Where files go. `~` is expanded. Must exist and be writable. |
| Move instead of copy | off | Moves the selection instead of copying it. |

## Manifest

```json
{
  "schemaVersion": 1,
  "id": "deck.inbox",
  "name": "Deck Inbox",
  "version": "1.0.0",
  "kinds": ["menu-action"],
  "menuActions": [{"id": "send", "label": "Copy to Decks", "glyph": "download",
                   "menus": ["file"],
                   "when": {"minItems": 1, "maxItems": 512, "folders": false}}]
}
```

The action is addressable as `deck.inbox:send`, so it can be bound in
`~/.config/flea/keys.toml` or run from a script:

```toml
[[ctrlshift]]
key = "D"
action = "deck.inbox:send"
```

```bash
flea --action deck.inbox:send
```

## Safety

A Flea plugin runs as your own user with your normal permissions; the `access`
list in the manifest is disclosure, not a sandbox. This one only reads the
selected paths and writes into the configured destination folder. The source of
this plugin is a single ~110-line Python file — read it before enabling.

## License

MIT. See [LICENSE](LICENSE).
