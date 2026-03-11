# oc

A fast, fuzzy session picker for [opencode](https://github.com/sst/opencode). Search your session history, preview conversations, and jump back into any session — right from your terminal.

![oc demo](https://github.com/user-attachments/assets/placeholder)

## Why

opencode stores every session in a local SQLite database but doesn't have a fast way to search across all of them and jump back in. `oc` fixes that — it queries the database directly and pipes everything through [fzf](https://github.com/junegunn/fzf) for instant fuzzy search with a live preview pane.

## Features

- Fuzzy search across all sessions by title, directory, project, or slug
- Live preview pane showing session metadata and recent messages
- Opens opencode in the correct directory with the selected session
- Fork sessions instead of continuing them (Ctrl-F)
- Copy session IDs to clipboard (Ctrl-Y)
- Plain text listing mode for scripting
- Zero dependencies beyond `sqlite3` and `fzf`

## Install

### Homebrew (macOS/Linux)

```bash
# coming soon
```

### Manual

```bash
# Clone the repo
git clone https://github.com/sunipan/oc.git
cd oc

# Copy to somewhere in your PATH
cp oc ~/.local/bin/oc

# Or symlink it
ln -sf "$(pwd)/oc" ~/.local/bin/oc
```

Make sure `~/.local/bin` is in your `PATH`. If not, add this to your shell profile (`.zshrc`, `.bashrc`, etc.):

```bash
export PATH="$HOME/.local/bin:$PATH"
```

### Requirements

- [opencode](https://github.com/sst/opencode) (the database must exist)
- [fzf](https://github.com/junegunn/fzf) — `brew install fzf`
- `sqlite3` — pre-installed on macOS and most Linux distros

## Usage

### Interactive search (default)

```bash
oc
```

Opens fzf with all sessions sorted by most recently updated. Type to fuzzy-filter by title, directory, project name, or slug.

### Pre-filtered search

```bash
oc "trading bot"
```

Opens fzf with the query pre-filled.

### List sessions

```bash
# List all sessions (plain text, no fzf)
oc ls

# Filter by keyword
oc ls myproject
```

### Preview a session

```bash
oc preview ses_348c86e20ffedk0dlLlZfiXz8W
```

Shows full session details: metadata, timestamps, and the last 10 messages.

### Keybindings

| Key | Action |
|---|---|
| `Enter` | Open session (`cd` to its directory + `opencode --session <id>`) |
| `Ctrl-F` | Fork the session instead of continuing it |
| `Ctrl-Y` | Copy the session ID to clipboard |
| `Esc` | Quit |

## How it works

opencode stores session data in a SQLite database at `~/.local/share/opencode/opencode.db`. The schema includes:

- **`session`** — id, title, directory, slug, timestamps, parent references
- **`message`** — role (user/assistant), timing, model info
- **`part`** — individual content parts (text, tool calls, tool results)
- **`project`** — git worktrees and project metadata

`oc` queries the `session`, `message`, `part`, and `project` tables directly to build the list and preview, then launches opencode with `--session <id>` in the session's original working directory.

## Configuration

### Environment variables

| Variable | Default | Description |
|---|---|---|
| `OPENCODE_DB` | `~/.local/share/opencode/opencode.db` | Path to the opencode SQLite database |

Override the database path if yours is in a non-standard location:

```bash
OPENCODE_DB=/path/to/opencode.db oc
```

## Display format

Each row in the picker shows:

```
TIME      TITLE                                                    DIR                MESSAGES
2h ago    Fix authentication bug in middleware                     ~/myproject        (24 msgs)
3d ago    Add dark mode support                                    ~/app              (156 msgs)
1w ago    Refactor database layer                                  ~/backend          (89 msgs)
```

The preview pane shows:

```
Fix authentication bug in middleware

  ID:        ses_348c86e20ffedk0dlLlZfiXz8W
  Slug:      swift-cactus
  Directory: /Users/you/myproject
  Project:   myproject
  Version:   1.2.18
  Messages:  24
  Created:   2026-03-01 14:30
  Updated:   2026-03-03 16:45 (2h ago)

── Last messages ──────────────────────────────

  user: Can you fix the auth middleware? It's not checking token expiry.

  assistant: I found the issue in middleware/auth.ts — the expiry check was comparing...
```

## License

MIT
