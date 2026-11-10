# vim-lists

A library that gives Vim plugins the same kind of list: a window at the bottom,
with a title line and rows, that opens, filters, refreshes and closes the same
way everywhere. It has no commands of its own, so you only need it when another
plugin requires it.

**Vim or Neovim:** tested on Vim 9.1 and newer. It's plain Vimscript, so it may
work on Neovim, but that's untested.

## Install

```vim
Plug 'vimoured/vim-lists'
```

## Keys in every list

The first line of a list shows its title and the keys it has. Each plugin adds
its own keys to these:

| Key | What it does |
| --- | --- |
| `r` | Refresh, and bring back everything `dd` hid. |
| `q` | Close. |
| `/` | Filter fuzzily as you type. `<CR>` keeps the filter, `<Esc>` drops what you changed. Only in lists that support filtering. |
| `dd` | Hide the item until `r`. In lists that support removing, remove it instead. |
| `u` | Bring back the item `dd` hid last. Not in lists where `dd` removes. |

## Use from a plugin

Open a list with `roumail_lists#open(name, spec, vars)`:

```vim
call roumail_lists#open('myplugin://things/' . name, {
      \ 'fetch': function('s:fetch'),
      \ 'heading': function('s:heading'),
      \ 'rows': function('s:rows'),
      \ 'id': {item -> item.name},
      \ 'keys': [{'key': '<CR>', 'hint': 'open', 'call': function('s:open')}],
      \ 'match': {item -> item.name},
      \ 'delete': function('s:delete'),
      \ }, {'myplugin_dir': dir})
```

| Argument | What it is |
| --- | --- |
| `name` | The name of the list's buffer. |
| `spec` | What the list shows and does, below. |
| `vars` | Buffer variables to set in the list, for `spec`'s functions to read. |

`spec` needs these:

| Key | What it is |
| --- | --- |
| `fetch()` | Returns the items, or `v:null` on failure, which leaves the list as it was. Runs on opening and on `r`. |
| `heading(items)` | Returns the lines above the rows. The first is the title, and the list adds the key hints to it. |
| `rows(items)` | Returns `[{'lines': [...], 'item': item}, ...]`, each item's lines in order. A row without `item` is a heading among the rows. |
| `id(item)` | Returns what stays the same for an item across fetches, so the cursor stays on it. |
| `keys` | The list's own keys, as `{'key': '<CR>', 'hint': 'open', 'call': Funcref}`. Leave out `call` to show a hint only. |

And these are optional:

| Key | What it is | Adds |
| --- | --- | --- |
| `match(item)` | Returns the text to filter on. | `/` |
| `delete(item)` | Removes the item from wherever `fetch()` finds it. The list is then fetched again. | `dd` removes instead of hiding, and there's no `u` |

From a list's keys, call these (all `roumail_lists#`):

| Function | What it does |
| --- | --- |
| `item()` | Returns the item under the cursor, or `{}` when there's none. |
| `row()` | Returns the row under the cursor, with anything extra you put in it, or `{}` on the title lines. |
| `refresh()` | Fetches the items again. The filter and the hidden items stay. |
| `delete()` | Removes or hides the item under the cursor, as `dd` does. |
| `set(items)` | Lists these items, without fetching. |
| `draw()` | Draws the items again, after a change to how they show. |
| `close()` | Closes the list. |

## Tests

The specs in [vim-suite](https://github.com/vimoured/vim-suite) define what it
does: `test/vim-lists.vim`.
