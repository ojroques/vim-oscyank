# vim-oscyank

A Vim plugin to yank to the system clipboard using the
[ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code#cite_note-23) OSC52
sequence.

When this sequence is emitted, the terminal will copy the given text to
the system clipboard. This is totally location independent, users can
potentially copy from anywhere including from remote SSH sessions.

The only requirement is that the terminal must support the sequence. As of
November 2020, here is the list of terminal emulators supporting or not OSC52
(not exhaustive):

| Terminal | OCS52 support |
|----------|:-------------:|
| [iTerm2](https://iterm2.com/) | **yes** |
| [kitty](https://github.com/kovidgoyal/kitty) | [**yes**](https://sw.kovidgoyal.net/kitty/protocol-extensions.html#pasting-to-clipboard) |
| [tmux](https://github.com/tmux/tmux) | **yes** |
| [Windows Terminal](https://github.com/microsoft/terminal) | **yes** |

## Installation
Using [vim-plug](https://github.com/junegunn/vim-plug):
```vim
call plug#begin()
Plug 'ojroques/vim-oscyank'
call plug#end()
```

## Usage

## Credits
The code is derived from
[hterm's script](https://github.com/chromium/hterm/blob/master/etc/osc52.vim)
and from [fcpg/vim-osc52](https://github.com/fcpg/vim-osc52).

[hterm's LICENCE](https://github.com/chromium/hterm/blob/master/LICENSE)<br/>
[LICENSE](LICENSE)
