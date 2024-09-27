# vim-oscyank

A Vim plugin to copy text to the system clipboard using the [ANSI
OSC52](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h3-Operating-System-Commands)
sequence.

The plugin wraps a piece of text inside an OSC52 sequence and writes it to Vim's
stderr. When your terminal detects the OSC52 sequence, it will copy the text
into the system clipboard.

This is totally location-independent, you can copy text from anywhere including
from remote SSH sessions. The only requirement is that the terminal must support
the sequence. A non-exhaustive list of the state of OSC52 integration in
popular terminal emulators [is here](https://can-i-use-terminal.github.io/features/osc52copy.html).

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

For Neovim, this plugin shouldn't be necessary anymore, since [Neovim contains native OSC 52 support since Neovim 10.0](https://github.com/neovim/neovim/pull/25872).
For older versions of Neovim, this plugin also works. However, you should instead check out [nvim-osc52](https://github.com/ojroques/nvim-osc52). Or
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

For instance, to automatically copy text that was yanked into the unnamed register (`"`)
as well as `+` and `"` when the clipboard isn't working:

```vim
if (!has('nvim') && !has('clipboard_working'))
    " In the event that the clipboard isn't working, it's quite likely that
    " the + and * registers will not be distinct from the unnamed register. In
    " this case, a:event.regname will always be '' (empty string). However, it
    " can be the case that `has('clipboard_working')` is false, yet `+` is
    " still distinct, so we want to check them all.
    let s:VimOSCYankPostRegisters = ['', '+', '*']
    function! s:VimOSCYankPostCallback(event)
        if a:event.operator == 'y' && index(s:VimOSCYankPostRegisters, a:event.regname) != -1
            call OSCYankRegister(a:event.regname)
        endif
    endfunction
    augroup VimOSCYankPost
        autocmd!
        autocmd TextYankPost * call s:VimOSCYankPostCallback(v:event)
    augroup END
endif
```
