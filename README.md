# vim-lists

The lists other Vim plugins show in a window at the bottom, all opened,
filtered, refreshed and closed the same way. Plugins open them; it has no
commands of its own.

**Vim or Neovim:** tested on Vim 9.1 and newer. It's plain Vimscript, so it may
work on Neovim, but that's untested.

## Install

```vim
Plug 'vimoured/vim-lists'
```

## Keys in every list

The first line is the list's title and keys. Each list adds its own.

| Key | What it does |
| --- | --- |
| `dd` | Remove the item, in lists that can. |
| `/` | Filter fuzzily as you type, in lists that can. `<CR>` keeps the filter, `<Esc>` drops what you changed. |
| `r` | Refresh. |
| `q` | Close. |

## For a plugin

```vim
call roumail_lists#open('myplugin://things/' . name, {
      \ 'fetch': function('s:fetch'),
      \ 'heading': function('s:heading'),
      \ 'rows': function('s:rows'),
      \ 'id': {item -> item.name},
      \ 'match': {item -> item.name},
      \ 'delete': function('s:delete'),
      \ 'keys': [{'key': '<CR>', 'hint': 'open', 'call': function('s:open')}],
      \ }, {'myplugin_dir': dir})
```

`match` and `delete` are optional. The other functions are `roumail_lists#item()`,
`#row()`, `#refresh()`, `#delete()`, `#set(items)`, `#draw()` and `#close()`.

## Tests

The specs in [vim-suite](https://github.com/vimoured/vim-suite) define what it
does: `test/vim-lists.vim`.
