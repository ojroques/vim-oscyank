# vim-oscyank

A Vim plugin to yank to the system clipboard using the
[ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code#cite_note-23) OSC52
sequence.

When this sequence is emitted, the terminal will copy the given text to
the system clipboard. This is totally location independent, users can
potentially copy from anywhere including from remote SSH sessions.

The only requirement is that the terminal must support the sequence. As of
November 2020, these terminals support OSC52 (not an exhaustive list):
* Windows Terminal

## Installation
Using [vim-plug](https://github.com/junegunn/vim-plug):
```vim
Plug 'ojroques/vim-oscyank'
```

## Usage

## Credits
The code is derived from
[hterm's script](https://github.com/chromium/hterm/blob/master/etc/osc52.vim)
and from [fcpg/vim-osc52](https://github.com/fcpg/vim-osc52).

[hterm's LICENCE](https://github.com/chromium/hterm/blob/master/LICENSE)

[LICENSE](LICENSE)
