# tmux Commands

Reference for tmux with a focus on split panes: creation, destruction, navigation.

> **Prefix key**: `Ctrl+b` by default. All `<prefix>` below means `Ctrl+b` (release, then press the next key). Many users rebind to `Ctrl+a` — check `~/.tmux.conf`.

---

## Panes — Primary Focus

A **pane** is a single terminal inside a **window**. A **window** lives inside a **session**.

### Creating splits

| Keys | Action |
|------|--------|
| `<prefix> %` | Split current pane **vertically** (side-by-side, left/right) |
| `<prefix> "` | Split current pane **horizontally** (stacked, top/bottom) |
| `tmux split-window -h` | Vertical split from CLI |
| `tmux split-window -v` | Horizontal split from CLI |
| `<prefix> :split-window -h -p 30` | Vertical split taking 30% of width |
| `<prefix> :split-window -c "#{pane_current_path}"` | Split but keep current working directory |

> Mnemonic: `%` looks like a vertical line, `"` looks like a horizontal stack.

### Navigating between panes

| Keys | Action |
|------|--------|
| `<prefix> ←` / `→` / `↑` / `↓` | Move focus in that direction |
| `<prefix> h` / `j` / `k` / `l` | Vim-style movement (if configured) |
| `<prefix> o` | Cycle to next pane |
| `<prefix> ;` | Toggle to last active pane |
| `<prefix> q` | Show pane numbers — press the number to jump |
| `<prefix> q 2` | Jump directly to pane 2 |

### Destroying panes

| Keys | Action |
|------|--------|
| `<prefix> x` | Kill current pane (prompts for confirmation) |
| `exit` or `Ctrl+d` | Close pane by ending the shell |
| `<prefix> !` | Break current pane into its own window |
| `tmux kill-pane -t 2` | Kill pane 2 from CLI |
| `<prefix> :kill-pane -a` | Kill **all other** panes in this window |

### Resizing panes

| Keys | Action |
|------|--------|
| `<prefix> Ctrl+←/→/↑/↓` | Resize by 1 cell |
| `<prefix> Alt+←/→/↑/↓` | Resize by 5 cells |
| `<prefix> z` | **Zoom** current pane to fullscreen (toggle) — very useful |
| `<prefix> :resize-pane -D 10` | Resize down 10 rows |
| `<prefix> :resize-pane -R 20` | Resize right 20 cols |

### Rearranging panes

| Keys | Action |
|------|--------|
| `<prefix> {` | Swap current pane with previous |
| `<prefix> }` | Swap current pane with next |
| `<prefix> Space` | Cycle through preset layouts (even-h, even-v, main-h, main-v, tiled) |
| `<prefix> Alt+1` … `Alt+5` | Apply layout 1–5 directly |

---

## Sessions

A session holds windows. Detach and reattach later with state intact.

| Command | Action |
|---------|--------|
| `tmux` | Start a new unnamed session |
| `tmux new -s work` | Start new session named `work` |
| `tmux ls` | List all sessions |
| `tmux attach -t work` | Attach to `work` session |
| `tmux a` | Attach to last session |
| `tmux kill-session -t work` | Kill session `work` |
| `tmux kill-server` | Kill ALL tmux sessions |
| `<prefix> d` | Detach from current session |
| `<prefix> s` | Interactive session picker |
| `<prefix> $` | Rename current session |
| `<prefix> (` / `)` | Switch to previous / next session |

---

## Windows

A window is a single "tab" inside a session, and can contain multiple panes.

| Keys | Action |
|------|--------|
| `<prefix> c` | Create new window |
| `<prefix> ,` | Rename current window |
| `<prefix> &` | Kill current window (with confirm) |
| `<prefix> n` / `p` | Next / previous window |
| `<prefix> 0`–`9` | Jump to window by number |
| `<prefix> w` | Interactive window picker |
| `<prefix> f` | Find window by text |
| `<prefix> .` | Move window (renumber) |

---

## Copy Mode (scrollback & selection)

| Keys | Action |
|------|--------|
| `<prefix> [` | Enter copy mode |
| `q` or `Esc` | Exit copy mode |
| `Space` | Start selection (vi mode) |
| `Enter` | Copy selection to tmux buffer |
| `<prefix> ]` | Paste last buffer |
| `<prefix> =` | Choose from buffer list |
| `/` or `?` | Search forward / backward in scrollback |
| `g` / `G` | Top / bottom of scrollback |

Set vi keys in `~/.tmux.conf`: `setw -g mode-keys vi`

---

## Common CLI Invocations

```bash
# Send keys to a pane (great for scripting)
tmux send-keys -t work:0.1 'npm run dev' C-m

# List panes in current session
tmux list-panes -a

# Show pane sizes / ids
tmux display -p '#{pane_index}: #{pane_width}x#{pane_height}'

# Source config without restart
tmux source-file ~/.tmux.conf
```

Within tmux: `<prefix> :` opens the command prompt, then type any command (e.g. `source-file ~/.tmux.conf`).

---

## Useful `~/.tmux.conf` Snippets

```tmux
# More intuitive split keys
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %

# Vim-style pane navigation
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Reload config
bind r source-file ~/.tmux.conf \; display "Config reloaded"

# Start windows & panes at 1, not 0
set -g base-index 1
setw -g pane-base-index 1

# Enable mouse (click panes, drag to resize, scroll)
set -g mouse on

# Bigger scrollback buffer
set -g history-limit 50000
```

---

## Cheat Sheet — Pane Essentials

```
Create:    <prefix> %       split vertical
           <prefix> "       split horizontal

Navigate:  <prefix> arrows  move focus
           <prefix> o       cycle
           <prefix> q N     jump to pane N

Destroy:   <prefix> x       kill pane
           <prefix> :kill-pane -a   kill all others

Resize:    <prefix> Ctrl+arrow
           <prefix> z       zoom/unzoom
```
