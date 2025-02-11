# vim-oscyank

A Vim plugin to copy text to the system clipboard using the
[ANSI OSC52](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h3-Operating-System-Commands)
sequence.

The plugin wraps a piece of text inside an OSC52 sequence and writes it to Vim's stderr. When your
terminal detects the OSC52 sequence, it will copy the text into the system clipboard.

This is totally location-independent, you can copy text from anywhere including from remote SSH
sessions. The only requirement is that the terminal must support the sequence. You can find at the
end a non-exhaustive list of the state of OSC52 integration in popular terminal emulators.

## Installation
With [vim-plug](https://github.com/junegunn/vim-plug) for instance:
```vim
Plug 'ojroques/vim-oscyank', {'branch': 'main'}
```

**If you are using Tmux**, run these steps first:
[Enabling OSC52 in Tmux](https://github.com/tmux/tmux/wiki/Clipboard#quick-summary). Then make sure
`set-clipboard` is set to `on` in your Tmux config:
```
set -s set-clipboard on
````
See `:h oscyank-tmux` for more details.

## Usage
Add this to your Vim config:
```vim
nmap <leader>c <Plug>OSCYankOperator
nmap <leader>cc <leader>c_
vmap <leader>c <Plug>OSCYankVisual
```

With these mappings:
* In normal mode, <kbd>\<leader\>c</kbd> is an operator that will copy the given text to the
  clipboard.
* In normal mode, <kbd>\<leader\>cc</kbd> will copy the current line.
* In visual mode, <kbd>\<leader\>c</kbd> will copy the current selection.

Neovim 0.10+ natively supports OSC52 since [this PR](https://github.com/neovim/neovim/pull/25872)
was merged. See `:h clipboard-osc52` in Neovim. If you still wish to use this plugin, add this to
your Neovim config:
```lua
vim.keymap.set('n', '<leader>c', '<Plug>OSCYankOperator')
vim.keymap.set('n', '<leader>cc', '<leader>c_', {remap = true})
vim.keymap.set('v', '<leader>c', '<Plug>OSCYankVisual')
```

## Configuration
The available options with their default values are:
```vim
let g:oscyank_max_length = 0  " maximum length of a selection, 0 for unlimited length
let g:oscyank_silent     = 0  " disable message on successful copy
let g:oscyank_trim       = 0  " trim surrounding whitespaces before copy
let g:oscyank_osc52      = "\x1b]52;c;%s\x07"  " the OSC52 format string to use
```

See `:h oscyank-config` for more details.

## Advanced Usage
The following commands are also available:
* `:OSCYank(text)`: copy text `text`
* `:OSCYankRegister(register)`: copy text from register `register`

For instance, to automatically copy text that was yanked into the unnamed register (`"`) as well as
`+` and `"` when the clipboard isn't working:

```vim
if (!has('nvim') && !has('clipboard_working'))
    " In the event that the clipboard isn't working, it's quite likely that
    " the + and * registers will not be distinct from the unnamed register. In
    " this case, a:event.regname will always be '' (empty string). However, it
    " can be the case that `has('clipboard_working')` is false, yet `+` is
    " still distinct, so we want to check them all.
    let s:VimOSCYankPostRegisters = ['', '+', '*']
    " copy text to clipboard on both (y)ank and (d)elete
    let s:VimOSCYankOperators = ['y', 'd']
    function! s:VimOSCYankPostCallback(event)
        if index(s:VimOSCYankPostRegisters, a:event.regname) != -1
            \ && index(s:VimOSCYankOperators, a:event.operator) != -1
            call OSCYankRegister(a:event.regname)
        endif
    endfunction
    augroup VimOSCYankPost
        autocmd!
        autocmd TextYankPost * call s:VimOSCYankPostCallback(v:event)
    augroup END
endif
```

## Terminal Support
| Terminal | OSC52 support |
|----------|:-------------:|
| [alacritty](https://github.com/alacritty/alacritty) | **yes** |
| [contour](https://github.com/contour-terminal/contour) | **yes** |
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

You can also check
[can-i-use-terminal.github.io](https://can-i-use-terminal.github.io/features/osc52copy.html) to see
if your terminal supports OSC52.
