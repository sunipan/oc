<p align="center">
  <h1 align="center">oc</h1>
  <p align="center">
    A fast, fuzzy session picker for <a href="https://github.com/sst/opencode">opencode</a>.<br/>
    Search thousands of sessions, preview conversations, and resume any session in one keystroke.
  </p>
</p>

<p align="center">
  <a href="#install">Install</a> &bull;
  <a href="#usage">Usage</a> &bull;
  <a href="#keybindings">Keybindings</a> &bull;
  <a href="#configuration">Configuration</a> &bull;
  <a href="#license">License</a>
</p>

---

## The Problem

opencode keeps every session in a local SQLite database, but there's no fast way to search through them and pick up where you left off. If you've accumulated hundreds (or thousands) of sessions across projects, finding the right one means scrolling through the TUI or guessing session IDs.

## The Solution

`oc` queries the database directly and pipes results through [fzf](https://github.com/junegunn/fzf) for instant fuzzy search with a live preview pane. Select a session and it opens opencode in the correct working directory, ready to go.

```
  oc> trading                                        < Fix authentication bug in middleware
  3h ago   Build trading bot backtester   ~/proj  (  >
  1d ago   Fix exchange API rate limits   ~/proj  (  >   ID:        ses_348c86e20ffe...
  3d ago   Add stop-loss strategy         ~/proj  (  >   Directory: /Users/you/proj
  1w ago   Research market data APIs      ~/proj  (  >   Messages:  24
                                                     >   Updated:   2h ago
  Enter: open | Ctrl-Y: copy ID | Ctrl-F: fork      >
                                                     >   user: Can you fix the auth
                                                     >   middleware? It's not checking
                                                     >   token expiry...
```

## Features

- **Fuzzy search** across sessions by title, directory, project, or slug
- **Live preview** with session metadata and recent messages
- **Automatic directory switching** — opens opencode in the session's original working directory
- **Session forking** — branch off from any previous session (Ctrl-F)
- **Clipboard support** — copy session IDs for scripting (Ctrl-Y)
- **Plain text mode** — `oc ls` for piping and scripting
- **Fast** — queries SQLite directly, no API calls or network requests

## Install

### Homebrew

```bash
brew install sunipan/tap/oc
```

### Manual

```bash
git clone https://github.com/sunipan/oc.git
cd oc
cp oc ~/.local/bin/    # or anywhere in your $PATH
```

### Requirements

| Dependency | Notes |
|---|---|
| [opencode](https://github.com/sst/opencode) | The local database must exist (`~/.local/share/opencode/opencode.db`) |
| [fzf](https://github.com/junegunn/fzf) | Installed automatically by Homebrew, or `brew install fzf` |
| sqlite3 | Pre-installed on macOS and most Linux distributions |

## Usage

```bash
# Interactive fuzzy search
oc

# Pre-filtered search
oc "auth middleware"

# Plain text list
oc ls

# Filtered list
oc ls myproject

# Show session details
oc preview <session_id>
```

Running `oc` with no arguments opens the interactive picker with all sessions sorted by most recently updated. Type to filter. Select a session and press Enter to open it.

## Keybindings

| Key | Action |
|---|---|
| Enter | Resume session in its original working directory |
| Ctrl-F | Fork the session (creates a new branch from that point) |
| Ctrl-Y | Copy session ID to clipboard |
| Esc | Quit |

## Configuration

| Variable | Default | Description |
|---|---|---|
| `OPENCODE_DB` | `~/.local/share/opencode/opencode.db` | Path to the opencode database |

```bash
# Use a custom database path
OPENCODE_DB=/path/to/opencode.db oc
```

## How It Works

opencode stores all session data in a local SQLite database. `oc` queries four tables:

| Table | Purpose |
|---|---|
| `session` | ID, title, directory, slug, timestamps |
| `message` | Conversation messages (user and assistant) |
| `part` | Message content (text, tool calls, results) |
| `project` | Git worktrees and project metadata |

When you select a session, `oc` runs:

```bash
cd <session_directory>
opencode --session <session_id>
```

For forked sessions (Ctrl-F), it adds `--fork` to create a new session branched from the selected one.

## License

[MIT](LICENSE)
