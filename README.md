# vim-oscyank

A Vim / Neovim plugin to copy text to the system clipboard using the [ANSI
OSC52](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h3-Operating-System-Commands)
sequence.

The plugin wraps a piece of text inside an OSC52 sequence and writes it to Vim's
stderr. When your terminal detects the OSC52 sequence, it will copy the text
into the system clipboard.

This is totally location-independent, you can copy text from anywhere including
from remote SSH sessions. The only requirement is that the terminal must support
the sequence. Here is a non-exhaustive list of the state of OSC52 integration in
popular terminal emulators:

| Terminal | OSC52 support |
|----------|:-------------:|
| [alacritty](https://github.com/alacritty/alacritty) | **yes** |
| [far2l](https://github.com/elfmz/far2l) | **yes** |
| [foot](https://codeberg.org/dnkl/foot) | **yes** |
| [gnome terminal](https://github.com/GNOME/gnome-terminal) (and other VTE-based terminals) | [not yet](https://gitlab.gnome.org/GNOME/vte/-/issues/2495) |
| [hterm](https://chromium.googlesource.com/apps/libapps/+/master/README.md) | [**yes**](https://chromium.googlesource.com/apps/libapps/+/master/nassh/doc/FAQ.md#Is-OSC-52-aka-clipboard-operations_supported) |
| [iterm2](https://iterm2.com/) | **yes** |
| [kitty](https://github.com/kovidgoyal/kitty) | **yes** |
| [konsole](https://konsole.kde.org/) | [not yet](https://bugs.kde.org/show_bug.cgi?id=372116) |
| [qterminal](https://github.com/lxqt/qterminal#readme) | [not yet](https://github.com/lxqt/qterminal/issues/839)
| [rxvt](http://rxvt.sourceforge.net/) | **yes** |
| [st](https://st.suckless.org/) | **yes** (but needs to be enabled, see [here](https://git.suckless.org/st/commit/a2a704492b9f4d2408d180f7aeeacf4c789a1d67.html)) |
| [terminal.app](https://en.wikipedia.org/wiki/Terminal_(macOS)) | no, but see [workaround](https://github.com/roy2220/osc52pty) |
| [tmux](https://github.com/tmux/tmux) | **yes** |
| [urxvt](http://software.schmorp.de/pkg/rxvt-unicode.html) | **yes** (with a script, see [here](https://github.com/ojroques/vim-oscyank/issues/4)) |
| [wezterm](https://github.com/wez/wezterm) | [**yes**](https://wezfurlong.org/wezterm/escape-sequences.html#operating-system-command-sequences) |
| [windows terminal](https://github.com/microsoft/terminal) | **yes** |
| [xterm.js](https://xtermjs.org/) (Hyper terminal) | [not yet](https://github.com/xtermjs/xterm.js/issues/3260) |
| [zellij](https://github.com/zellij-org/zellij/) | **yes** |

Feel free to add terminals to this list by submitting a pull request.

## Installation
With [vim-plug](https://github.com/junegunn/vim-plug) for instance:
```vim
Plug 'ojroques/vim-oscyank', {'branch': 'main'}
```

**If you are using tmux**, run these steps first: [enabling OSC52 in
tmux](https://github.com/tmux/tmux/wiki/Clipboard#quick-summary). Then make sure
`set-clipboard` is set to `on`: `set -s set-clipboard on`. See `:h oscyank-tmux`
for more details.

## Usage
Add this to your Vim config:
```vim
nmap <leader>c <Plug>OSCYankOperator
nmap <leader>cc <leader>c_
vmap <leader>c <Plug>OSCYankVisual
```

Using these mappings:
* In normal mode, <kbd>\<leader\>c</kbd> is an operator that will copy the given
  text to the clipboard.
* In normal mode, <kbd>\<leader\>cc</kbd> will copy the current line.
* In visual mode, <kbd>\<leader\>c</kbd> will copy the current selection.

For Neovim check out [nvim-osc52](https://github.com/ojroques/nvim-osc52). Or
add this to your Neovim config:
```lua
vim.keymap.set('n', '<leader>c', '<Plug>OSCYankOperator')
vim.keymap.set('n', '<leader>cc', '<leader>c_', {remap = true})
vim.keymap.set('v', '<leader>c', '<Plug>OSCYankVisual')
```

## Configuration
The available options with their default values are:
```vim
let g:oscyank_max_length = 0  " maximum length of a selection
let g:oscyank_silent     = 0  " disable message on successful copy
let g:oscyank_trim       = 0  " trim surrounding whitespaces before copy
let g:oscyank_osc52      = "\x1b]52;c;%s\x07"  " the OSC52 format string to use
```

See `:h oscyank-config` for more details.

## Advanced Usage
The following commands are also available:
* `:OSCYank(text)`: copy text `text`
* `:OSCYankRegister(register)`: copy text from register `register`

For instance, to automatically copy text that was yanked into register `+`:
```vim
autocmd TextYankPost *
    \ if v:event.operator is 'y' && v:event.regname is '+' |
    \ execute 'OSCYankRegister +' |
    \ endif
```
