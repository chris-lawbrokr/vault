# Neovim Commands

Comprehensive reference for Neovim — modes, movement, editing, windows, buffers, search, and common Lua config patterns.

---

## Modes

| Mode | Enter from Normal | Purpose |
|------|-------------------|---------|
| **Normal** | `Esc` / `Ctrl+[` | Navigation and commands (default) |
| **Insert** | `i` `a` `o` `I` `A` `O` | Type text |
| **Visual** | `v` (char) `V` (line) `Ctrl+v` (block) | Select text |
| **Command** | `:` | Run ex commands |
| **Replace** | `R` | Overwrite text |
| **Terminal** | `:terminal` then `i` | Run shell inside nvim |

> Always `Esc` back to Normal before running commands. `Ctrl+[` is the ergonomic alternative.

---

## Movement (Normal mode)

### Basic

| Key | Motion |
|-----|--------|
| `h` `j` `k` `l` | Left, down, up, right |
| `w` / `W` | Next word / WORD (WORD = whitespace-delimited) |
| `b` / `B` | Previous word / WORD |
| `e` / `E` | End of word / WORD |
| `0` | Start of line |
| `^` | First non-blank on line |
| `$` | End of line |
| `gg` | Top of file |
| `G` | Bottom of file |
| `{N}G` or `:{N}` | Go to line N |
| `Ctrl+u` / `Ctrl+d` | Half page up / down |
| `Ctrl+b` / `Ctrl+f` | Full page up / down |
| `H` / `M` / `L` | Top / middle / bottom of screen |
| `zz` / `zt` / `zb` | Scroll current line to center / top / bottom |

### Find on line

| Key | Motion |
|-----|--------|
| `f{char}` | Jump forward to next `{char}` |
| `F{char}` | Jump backward |
| `t{char}` | Jump up to (not onto) next `{char}` |
| `;` / `,` | Repeat last `f`/`t` forward / backward |

### Jumps

| Key | Motion |
|-----|--------|
| `%` | Matching bracket / paren / brace |
| `*` / `#` | Search word under cursor forward / backward |
| `Ctrl+o` | Jump back (jumplist) |
| `Ctrl+i` | Jump forward |
| `gd` | Go to local definition |
| `gD` | Go to global definition |
| `''` | Return to position before last jump |

---

## Editing

### Insert entry points

| Key | Action |
|-----|--------|
| `i` | Insert before cursor |
| `I` | Insert at start of line |
| `a` | Append after cursor |
| `A` | Append at end of line |
| `o` | New line below, enter Insert |
| `O` | New line above, enter Insert |

### Delete / change / yank

Pattern: `{operator}{motion}` or `{operator}{operator}` for whole line.

| Keys | Action |
|------|--------|
| `x` | Delete char under cursor |
| `dd` | Delete (cut) line |
| `dw` | Delete word |
| `d$` or `D` | Delete to end of line |
| `daw` | Delete a word (with surrounding space) |
| `dip` | Delete inner paragraph |
| `cc` / `C` | Change line / to end of line |
| `ciw` | Change inner word |
| `ci"` | Change inside quotes |
| `ci(` / `cib` | Change inside parentheses |
| `ci{` / `ciB` | Change inside braces |
| `cit` | Change inside HTML/XML tag |
| `yy` or `Y` | Yank (copy) line |
| `yw` | Yank word |
| `yip` | Yank inner paragraph |
| `p` / `P` | Paste after / before cursor |
| `r{char}` | Replace single character |
| `u` | Undo |
| `Ctrl+r` | Redo |
| `.` | Repeat last change |

### Text objects

Use with operators: `d`, `c`, `y`, `v`.

| Object | Meaning |
|--------|---------|
| `iw` / `aw` | inner word / a word |
| `is` / `as` | inner sentence / a sentence |
| `ip` / `ap` | inner paragraph / a paragraph |
| `i"` `i'` `` i` `` | inside quotes |
| `i(` `i)` `ib` | inside parens |
| `i{` `i}` `iB` | inside braces |
| `i[` `i]` | inside brackets |
| `it` / `at` | inside / around tag |

### Indent / case / formatting

| Keys | Action |
|------|--------|
| `>>` / `<<` | Indent / outdent line |
| `>ip` | Indent inner paragraph |
| `=` | Auto-indent (e.g. `=ap`, `gg=G` for whole file) |
| `~` | Toggle case of char |
| `guu` / `gUU` | Lowercase / uppercase line |
| `J` | Join line below to current |

---

## Visual Mode Selection

| Key | Action |
|-----|--------|
| `v` | Character-wise select |
| `V` | Line-wise select |
| `Ctrl+v` | Block (column) select |
| `gv` | Re-select last visual selection |
| `o` | Move to other end of selection |
| `d` / `c` / `y` | Delete / change / yank selection |
| `>` / `<` | Indent / outdent selection |
| `:` | Run ex command on selection |

**Block edit trick**: `Ctrl+v`, select column, `I`, type text, `Esc` — text applied to all lines.

---

## Search & Replace

| Command | Action |
|---------|--------|
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` / `N` | Next / previous match |
| `:noh` | Clear search highlight |
| `:%s/foo/bar/g` | Replace all `foo` with `bar` in file |
| `:%s/foo/bar/gc` | Same, with confirmation |
| `:'<,'>s/foo/bar/g` | Replace in visual selection |
| `:s/foo/bar/g` | Replace in current line |
| `:%s/\<word\>/new/g` | Match whole word only |

Regex flavor: `\v` at start of pattern enables "very magic" for familiar regex syntax.

---

## Buffers, Windows, Tabs

### Buffers (files loaded in memory)

| Command | Action |
|---------|--------|
| `:e {file}` | Edit/open file |
| `:w` | Write (save) |
| `:w {file}` | Write as |
| `:q` | Quit |
| `:wq` or `:x` or `ZZ` | Write and quit |
| `:q!` or `ZQ` | Quit without saving |
| `:ls` or `:buffers` | List buffers |
| `:b {N}` or `:b {name}` | Switch to buffer |
| `:bd` | Delete buffer |
| `:bn` / `:bp` | Next / previous buffer |

### Windows (splits)

| Keys | Action |
|------|--------|
| `:sp` or `Ctrl+w s` | Horizontal split |
| `:vsp` or `Ctrl+w v` | Vertical split |
| `Ctrl+w h/j/k/l` | Move focus between windows |
| `Ctrl+w w` | Cycle windows |
| `Ctrl+w q` or `:q` | Close window |
| `Ctrl+w o` | Close all other windows |
| `Ctrl+w =` | Equalize window sizes |
| `Ctrl+w _` | Maximize height |
| `Ctrl+w |` | Maximize width |
| `Ctrl+w H/J/K/L` | Move window far left/down/up/right |
| `Ctrl+w +` / `-` | Grow / shrink height |
| `Ctrl+w >` / `<` | Grow / shrink width |

### Tabs (each holds its own set of windows)

| Command | Action |
|---------|--------|
| `:tabnew` | New tab |
| `:tabedit {file}` | Open file in new tab |
| `gt` / `gT` | Next / previous tab |
| `{N}gt` | Go to tab N |
| `:tabclose` | Close tab |
| `:tabonly` | Close all other tabs |

---

## Macros & Registers

| Keys | Action |
|------|--------|
| `q{reg}` | Start recording macro into register (e.g. `qa`) |
| `q` | Stop recording |
| `@{reg}` | Play macro (`@a`) |
| `@@` | Replay last macro |
| `{N}@a` | Run macro N times |
| `"{reg}y` | Yank into register |
| `"{reg}p` | Paste from register |
| `:reg` | Show all registers |

Special registers: `"` default, `0` last yank, `+` system clipboard, `*` selection, `/` last search, `%` current file.

---

## LSP (built-in in Neovim)

Keybinds below are common defaults — check your config (many set custom maps).

| Keys | Action |
|------|--------|
| `gd` | Go to definition |
| `gD` | Go to declaration |
| `gr` | References |
| `gi` | Implementation |
| `K` | Hover docs |
| `Ctrl+k` (insert) | Signature help |
| `<leader>rn` | Rename symbol |
| `<leader>ca` | Code action |
| `[d` / `]d` | Previous / next diagnostic |
| `<leader>f` | Format buffer |
| `:LspInfo` | LSP status |
| `:Mason` | Mason package manager UI (if installed) |

---

## Terminal Mode

| Command | Action |
|---------|--------|
| `:terminal` | Open terminal in buffer |
| `Ctrl+\ Ctrl+n` | Exit terminal mode to Normal |
| `i` / `a` | Re-enter terminal insert mode |
| `:!cmd` | Run shell command once |
| `:r !cmd` | Read command output into buffer |

---

## File Explorer (built-in netrw)

| Command | Action |
|---------|--------|
| `:Ex` | Open explorer in current window |
| `:Vex` / `:Sex` | Open vertical / horizontal explorer |
| `:e .` | Open explorer at cwd |

Most users use a plugin (nvim-tree, neo-tree, oil.nvim) instead.

---

## Useful Ex Commands

| Command | Action |
|---------|--------|
| `:set number` / `:set nonumber` | Toggle line numbers |
| `:set relativenumber` | Relative numbers |
| `:set wrap` / `:set nowrap` | Toggle word wrap |
| `:set ignorecase` | Case-insensitive search |
| `:set smartcase` | Case-sensitive if uppercase present |
| `:set paste` / `:set nopaste` | Toggle paste mode |
| `:cd {path}` | Change working directory |
| `:pwd` | Show working directory |
| `:sort` | Sort visually selected lines |
| `:%!jq .` | Filter whole buffer through `jq` |
| `:source %` | Source current file (for Lua/vimscript config) |
| `:checkhealth` | Run diagnostic checks |

---

## Help System

| Command | Action |
|---------|--------|
| `:help` | Main help |
| `:help {topic}` | Specific topic (try `:help text-objects`) |
| `:help {key}` | Keybind help — e.g. `:help ciw` |
| `K` on a word in help | Follow tag under cursor |
| `Ctrl+]` / `Ctrl+t` | Follow / return from tag |

---

## Config Essentials (Lua)

Config lives in `~/.config/nvim/` — entry point is `init.lua`.

```lua
-- Leader key
vim.g.mapleader = " "

-- Common options
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.tabstop = 2
vim.opt.shiftwidth = 2
vim.opt.expandtab = true
vim.opt.smartindent = true
vim.opt.wrap = false
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.termguicolors = true
vim.opt.clipboard = "unnamedplus"  -- use system clipboard
vim.opt.undofile = true
vim.opt.signcolumn = "yes"

-- Example keymaps
vim.keymap.set("n", "<leader>w", ":w<CR>", { desc = "Save" })
vim.keymap.set("n", "<leader>q", ":q<CR>", { desc = "Quit" })
vim.keymap.set("n", "<C-h>", "<C-w>h")
vim.keymap.set("n", "<C-j>", "<C-w>j")
vim.keymap.set("n", "<C-k>", "<C-w>k")
vim.keymap.set("n", "<C-l>", "<C-w>l")
```

### Plugin managers

- **lazy.nvim** — most popular, lazy-loads plugins
- **packer.nvim** — older, still widely used

---

## Cheat Sheet — Daily Essentials

```
Save/quit:     :w  :q  :wq  :q!
Undo/redo:     u   Ctrl+r
Repeat:        .
Search:        /pattern   n   N
Replace all:   :%s/old/new/g
Jump lines:    gg   G   {N}G
Jump back:     Ctrl+o   Ctrl+i
Word ops:      ciw   daw   yiw
Line ops:      dd   yy   cc   >>   <<
Splits:        :vsp   :sp   Ctrl+w h/j/k/l
Buffers:       :ls   :bn   :bp   :bd
LSP:           gd   K   gr   <leader>rn
```
