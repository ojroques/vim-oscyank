# vim-oscyank

A Vim plugin to copy text to the system clipboard from anywhere using the
[ANSI OCS52](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h3-Operating-System-Commands)
sequence.

When this sequence is emitted by Vim, the terminal will copy the given text
into the system clipboard. **This is totally location independent**, users can
copy from anywhere *including from remote SSH sessions*.

The only requirement is that the terminal must support the sequence. Here is
a non-exhaustive list of terminal emulators supporting or not OSC52 (as of
November 2020):

| Terminal | OCS52 support |
|----------|:-------------:|
| [Alacritty](https://github.com/alacritty/alacritty) | **yes** |
| [GNOME Terminal](https://github.com/GNOME/gnome-terminal) | [not yet](https://gitlab.gnome.org/GNOME/vte/-/issues/125) |
| [hterm (Chromebook)](https://chromium.googlesource.com/apps/libapps/+/master/README.md) | [**yes**](https://chromium.googlesource.com/apps/libapps/+/master/nassh/doc/FAQ.md#Is-OSC-52-aka-clipboard-operations_supported) |
| [iTerm2](https://iterm2.com/) | **yes** |
| [kitty](https://github.com/kovidgoyal/kitty) | [**yes**](https://sw.kovidgoyal.net/kitty/protocol-extensions.html#pasting-to-clipboard) |
| [rxvt](http://rxvt.sourceforge.net/) & [rxvt-unicode](http://software.schmorp.de/pkg/rxvt-unicode.html) | **yes** |
| [screen](https://www.gnu.org/software/screen/) | **yes** |
| [tmux](https://github.com/tmux/tmux) | **yes** |
| [Windows Terminal](https://github.com/microsoft/terminal) | **yes** |
| [xterm](https://invisible-island.net/xterm/) | **yes** |

## Installation
Using [vim-plug](https://github.com/junegunn/vim-plug):
```vim
call plug#begin()
Plug 'ojroques/vim-oscyank'
call plug#end()
```

## Usage
Enter Visual mode, select your text and run `:OSCYank<CR>`.

Or map the command in your config:
```vim
vnoremap <leader>c :OSCYank<CR>
```

By default you can copy up to 100000 characters at once. If your terminal
supports it, you can raise this limit with:
```vim
let g:oscyank_max_length = 1000000
```

The plugin treats *tmux*, *screen* and *kitty* differently than other terminal
emulators. The plugin automatically detects when one of these terminal is used
but you can force that behavior with:
```vim
let g:oscyank_term = 'tmux'  " valid: 'screen', 'kitty'
```

## Credits
The code is derived from
[hterm's script](https://github.com/chromium/hterm/blob/master/etc/osc52.vim).

[hterm's LICENCE](https://github.com/chromium/hterm/blob/master/LICENSE)<br/>
[LICENSE](LICENSE)
