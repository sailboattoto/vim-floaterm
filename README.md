![CI](https://github.com/voldikss/vim-floaterm/workflows/CI/badge.svg)

【[Introduction in Chinese|中文文档](https://zhuanlan.zhihu.com/p/107749687)】

Use neovim terminal in the floating window.

![](https://user-images.githubusercontent.com/20282795/74799912-de268200-530c-11ea-9831-d412a7700505.png)

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
  - [Global variables](#global-variables)
  - [Commands](#commands)
  - [Keymaps](#keymaps)
  - [Change highlight](#change-highlight)
- [More use cases and demos](#more-use-cases-and-demos)
  - [General](#general)
  - [Use as the git editor](#use-as-the-git-editor)
  - [Use as an fzf plugin](#use-as-an-fzf-plugin)
  - [Use as an fff plugin](#use-as-an-fff-plugin)
  - [Use as an nnn plugin](#use-as-an-nnn-plugin)
  - [Use as an lf plugin](#use-as-an-lf-plugin)
  - [Use as a ranger plugin](#use-as-a-ranger-plugin)
  - [Use as a vifm plugin](#use-as-a-vifm-plugin)
  - [Use as a Python REPL plugin](#use-as-a-python-repl-plugin)
  - [Use with other command line tools](#use-with-other-command-line-tools)
  - [Integrate with vim-clap](#integrate-with-vim-clap)
  - [Integrate with denite.nvim](#integrate-with-denitenvim)
  - [Integrate with coc.nvim](#integrate-with-cocnvim)
  - [Integrate with asynctasks.vim](#integrate-with-asynctasksvim)
- [How to define more wrappers](#how-to-define-more-wrappers)
- [How to write sources for fuzzy finder plugins](#how-to-write-sources-for-fuzzy-finder-plugins)
- [F.A.Q](#f.a.q)
- [Credits](#credits)
- [License](#license)

## Features

- Floating window support
- Open and toggle terminal window quickly
- Multiple terminal instances
- Customizable floating terminal style
- Switch/Preview floating terminal buffers using [vim-clap](https://github.com/liuchengxu/vim-clap), [denite.nvim](https://github.com/Shougo/denite.nvim) or [coc.nvim](https://github.com/neoclide/coc.nvim)
- Integrate with other external command-line tools(ranger, lf, fzf, etc.)
- Use as a custom task runner for [asynctasks.vim](https://github.com/skywind3000/asynctasks.vim)

## Requirements

- Vim or NeoVim with `terminal` feature

- NeoVim supporting `floating window` is better, but not necessary

## Installation

- vim-plug

```vim
Plug 'voldikss/vim-floaterm'
```

- dein.nvim

```vim
call dein#add('voldikss/vim-floaterm')
```

## Usage

Use `:FloatermNew` command to open a terminal window, `:FloatermToggle` to hide/reopen that. The filetype of the terminal buffer is set to `floaterm`.

Generally just one floaterm instance is enough. If you've opened more than one floaterm instances, they are attached to a double-circular-linkedlist. Then you can use `:FloatermNext` or `:FloatermPrev` to switch between them.

### Commands

- `:FloatermNew [cmd]` Open a floating terminal window, if `cmd` exists, it will be executed after shell startup

- `:FloatermToggle` Open or hide the floaterm window

- `:FloatermPrev` Switch to the previous floaterm window

- `:FloatermNext` Switch to the next floaterm window

- `:FloatermSend` Send selected lines to a job in floaterm. Typically this command is executed with a range, i.e., `:'<,'>FloatermSend`, if no ranges, send the current line. Also you may try `:FloatermSend!` and `:'<,'>FloatermSend!`, the former trims the whitespace in the begin of the line, and the latter removes the whitespace but still keeps the indent.

## Global variables

- **`g:floaterm_type`**

  Type `string`. `'floating'`(neovim only) by default. Set it to `'normal'` if your vim/nvim doesn't support `floatwin` or you don't like floating window.

- **`g:floaterm_width`**

  Type `int` (number of columns) or `float` (between 0 and 1). If `float`, the width is relative to `&columns`. Default: `0.6`

- **`g:floaterm_height`**

  Type `int` (number of lines) or `float` (between 0 and 1). If `float`, the height is relative to `&lines`. Default: `0.6`

- **`g:floaterm_winblend`**

  Type `int`. The transparency of the floating terminal. Default: `0`

- **`g:floaterm_position`**

  Type `string`. The position of the floating window. Available: `'center'`, `'topleft'`, `'topright'`, `'bottomleft'`, `'bottomright'`, `'auto'(at the cursor place)`. Default: `'center'`

- **`g:floaterm_borderchars`**

  Type `array of string`. Characters of the floating window border.

  Default: `['─', '│', '─', '│', '┌', '┐', '┘', '└']`

- **`g:floaterm_rootmarkers`**

  Type `array of string`. If not empty, floaterm will be opened in the project root directory.

  Example: `['.project', '.git', '.hg', '.svn', '.root', '.gitignore']`, Default: `[]`

- **`g:floaterm_autoinsert`**

  Type `bool`. Enter terminal mode after opening a floaterm. Default: `v:true`

- **`g:floaterm_open_command`**

  Type `string`. Command used for opening a file from within `:terminal`.

  Available: `'edit'`, `'split'`, `'vsplit'`, `'tabe'`, `'drop'`. Default: `'edit'`

- **`g:floaterm_gitcommit`**

  Type `string`. Opening strategy for running `git commit` in floaterm window.

  Available: `'floaterm'`(open `gitcommit` file in the floaterm window), `'split'`, `'vsplit'`, `'tabe'`.

  Default: `v:null` which means this is disabled by default(use your own `$GIT_EDITOR`).

### Keymaps

This plugin doesn't supply any default mappings. To use a recommended mappings, put the following code in your `vimrc`.

```vim
""" Configuration example
let g:floaterm_keymap_new    = '<F7>'
let g:floaterm_keymap_prev   = '<F8>'
let g:floaterm_keymap_next   = '<F9>'
let g:floaterm_keymap_toggle = '<F10>'
```

You can also use other keys as shown below:

```vim
let g:floaterm_keymap_new = '<Leader>fn'
```

Note that this key mapping is installed from the [plugin](./plugin) directory, so if you use on-demand loading provided by some plugins manager, the keymap won't take effect. Actually you don't need the on-demand loading for this plugin as it even doesn't slow your startup.

### Change highlight

This plugin supplies two `highlight-groups` to specify the background/foreground color of floaterm (also the border color if `g:floaterm_type` is `'floating'`) window.

By default, they are both linked to `NormalFloat`. To customize, use `hi` command together with the colors you prefer.

```vim
" Configuration example

" Set floaterm window's background to black
hi FloatermNF guibg=black
" Set floating window border line color to cyan, and background to orange
hi FloatermBorderNF guibg=orange guifg=cyan
```

![](https://user-images.githubusercontent.com/20282795/74794098-42d9e080-52fd-11ea-9ccf-661dd748aa03.png)

## More use cases and demos

vim-floaterm is a nvim/vim terminal plugin, it can run all the command-line programs in the terminal even `nvim/vim` itself.

**❗️Note**: The following cases should work both in NeoVim and Vim, if some of them don't work on Vim, try startuping Vim with `--servername` argument, i.e., `vim --servername /tmp/vimtmpfile`

### General

Requirements: `pip3 install neovim-remote`

Normally if you run `vim/nvim somefile.txt` within a builtin terminal, you will get another nvim/vim instance running in the subprocess. This plugin allows you to open files from within `:terminal` without starting a nested nvim process. To archive that, just replace `vim/nvim` with `floaterm`, i.e., `floaterm somefile.txt`

![](https://user-images.githubusercontent.com/20282795/74755351-06cb5f00-52ae-11ea-84ba-d0b3e88e9377.gif)

### Use as the git editor

See `g:floaterm_gitcommit` option.

Execute `git commit` in the terminal window without starting a nested nvim.

![](https://user-images.githubusercontent.com/20282795/76213003-b0b26180-6244-11ea-85ad-1632adfd07d9.gif)

### Use as an fzf plugin

This plugin has implemented a [wrapper](./autoload/floaterm/wrapper/fzf.vim) for fzf command. So it can be used as a tiny fzf plugin.

Try `:FloatermNew fzf` or even wrap this to a new command like this:

```vim
command! FZF FloatermNew fzf
```

![](https://user-images.githubusercontent.com/20282795/78089550-60b95b80-73fa-11ea-8ac8-8fab2025b4d8.gif)

### Use as an fff plugin

There is also an [fff wrapper](./autoload/floaterm/wrapper/fff.vim)

Try `:FloatermNew fff` or define a new command:

```vim
command! FFF FloatermNew fff
```

![](https://user-images.githubusercontent.com/1472981/75105718-9f315d00-567b-11ea-82d1-6f9a6365391f.gif)

### Use as an nnn plugin

There is also an [nnn wrapper](./autoload/floaterm/wrapper/nnn.vim)

Try `:FloatermNew nnn` or define a new command:

```vim
command! NNN FloatermNew nnn
```

![](https://user-images.githubusercontent.com/20282795/75599726-7a594180-5ae2-11ea-80e2-7a33df1433f6.gif)

### Use as an lf plugin

There is also an [lf wrapper](./autoload/floaterm/wrapper/lf.vim)

Try `:FloatermNew lf` or define a new command:

```vim
command! LF FloatermNew lf
```

![](https://user-images.githubusercontent.com/20282795/77142551-6e4a1980-6abb-11ea-9525-73e1a1844e83.gif)

### Use as a ranger plugin

This plugin can also be a handy ranger plugin since it also has a [ranger wrapper](./autoload/floaterm/wrapper/ranger.vim)

Try `:FloatermNew ranger` or define a new command:

```vim
command! Ranger FloatermNew ranger
```

![](https://user-images.githubusercontent.com/20282795/74800026-2e054900-530d-11ea-8e2a-67168a9532a9.gif)

### Use as a Vifm plugin

There is also a [vifm wrapper](./autoload/floaterm/wrapper/vifm.vim)

Try `:FloatermNew vifm` or define a new command:

```vim
command! Vifm FloatermNew vifm
```

![](https://user-images.githubusercontent.com/43941510/77137476-3c888100-6ac2-11ea-90f2-2345c881aa8f.gif)

### Use as a Python REPL plugin

Use `:FloatermNew python` to open a python REPL. After that you can use `:FloatermSend` to send lines to the Python interactive shell.

This can also work for other languages which have interactive shells, such as lua, node, etc.

![](https://user-images.githubusercontent.com/20282795/75602790-227f0280-5b03-11ea-9411-67dd1895a66b.gif)

### Use with other command line tools

Furthermore, you can also use other command-line programs, such as lazygit, htop, ncdu, etc.

Use `lazygit` for instance:

![](https://user-images.githubusercontent.com/20282795/74755376-0f239a00-52ae-11ea-9261-44d94abe5924.png)

### Integrate with [vim-clap](https://github.com/liuchengxu/vim-clap)

Use vim-clap to switch/preview floating terminal buffers.

Try `:Clap floaterm`

![](https://user-images.githubusercontent.com/20282795/74755336-00d57e00-52ae-11ea-8afc-030ff55c2145.gif)

### Integrate with [denite.nvim](https://github.com/Shougo/denite.nvim)

Use denite to switch/preview/open floating terminal buffers.

Try `:Denite floaterm`

![](https://user-images.githubusercontent.com/1239245/73604753-17ef4d00-45d9-11ea-967f-ef75927e2beb.gif)

### Integrate with [coc.nvim](https://github.com/neoclide/coc.nvim)

Use CocList to switch/preview/open floating terminal buffers.

Install [coc-floaterm](https://github.com/voldikss/coc-floaterm) and try `:CocList floaterm`

![](https://user-images.githubusercontent.com/20282795/75005925-fcc27f80-54aa-11ea-832e-59ea5b02fc04.gif)

### Integrate with [asynctasks.vim](https://github.com/skywind3000/asynctasks.vim)

This plugin already has a builtin runner for [asynctasks.vim](https://github.com/skywind3000/asynctasks.vim/). To use it, change `g:asynctasks_term_pos` to `"floaterm"` or add a `"pos=floaterm"` filed in your asynctasks configuration files. Then your task will be ran in the floaterm window. See asynctasks.vim [WIKI](https://github.com/skywind3000/asynctasks.vim/wiki/Customize-Runner) for more information.

If you are using on-demand loading, you need to copy the following lines to your `vimrc` to make it work.

```vim
function! s:runner_proc(opts)
  let curr_bufnr = floaterm#curr()
  if has_key(a:opts, 'silent') && a:opts.silent == 1
    call floaterm#hide()
  endif
  let cmd = 'cd ' . shellescape(getcwd())
  call floaterm#terminal#send(curr_bufnr, [cmd])
  call floaterm#terminal#send(curr_bufnr, [a:opts.cmd])
  stopinsert
  if &filetype == 'floaterm' && g:floaterm_autoinsert
    startinsert
  endif
endfunction

let g:asyncrun_runner = get(g:, 'asyncrun_runner', {})
let g:asyncrun_runner.floaterm = function('s:runner_proc')
```

## How to define more wrappers

There are two ways for a command to be spawned:

- To be executed after `&shell` was startup. see [fzf wrapper](./autoload/floaterm/wrapper/fzf.vim)

  ```vim
  function! floaterm#wrapper#fzf#() abort
    return ['floaterm $(fzf)', {}, v:true]
  endfunction
  ```

  The code above returns an array. `floaterm $(fzf)` is the command to be executed. `v:true` means the command will be executed after the `&shell` startup.

- To be executed through `termopen()`/`term_start()` function, in this case, a callback function is allowed. See [ranger wrapper](./autoload/floaterm/wrapper/ranger.vim)

  ```vim
  function! floaterm#wrapper#ranger#() abort
    let s:ranger_tmpfile = tempname()
    let cmd = 'ranger --choosefiles=' . s:ranger_tmpfile
    return [cmd, {'on_exit': funcref('s:ranger_callback')}, v:false]
  endfunction

  function! s:ranger_callback(...) abort
    if filereadable(s:ranger_tmpfile)
      let filenames = readfile(s:ranger_tmpfile)
      if !empty(filenames)
        call floaterm#hide()
        for filename in filenames
          execute 'edit ' . fnameescape(filename)
        endfor
      endif
    endif
  endfunction
  ```

  Here `v:false` means `cmd` will be passed through `termopen()`(neovim) or `term_start()`(vim). Function `s:ranger_callback()` will be invoked when the `cmd` exits.

## How to write sources for fuzzy finder plugins

Function `floaterm#buflist#gather()` returns a list contains all the floaterm buffers.

Function `floaterm#terminal#open({bufnr})` opens the floaterm whose buffer number is `bufnr`.

For reference, see [floaterm source for vim-clap](./autoload/clap/provider/floaterm.vim).

## F.A.Q

- #### This plugin leaves an empty buffer on startify window

  Put this code in your `vimrc`

  ```vim
  autocmd User Startified setlocal buflisted
  ```

- #### I want to use another shell in the terminal. (e.g., Use fish instead of bash)

  Set `shell` option in your `vimrc`:

  ```vim
  set shell=/path/to/shell
  ```

- #### I would like to customize the style of the floating terminal window

  Use `autocmd`. For example

  ```vim
  function s:floatermSettings()
      setlocal number
      " more settings
  endfunction

  autocmd FileType floaterm call s:floatermSettings()
  ```

- #### I want to open normal floaterm in the vsplit window.

  Use `:wincmd H` or `:wincmd L`. If you want a persistent layout, register an `autocmd`:

  ```vim
  autocmd FileType floaterm wincmd H
  ```

## Credits

- [floaterm executable](https://github.com/voldikss/vim-floaterm/blob/master/bin/floaterm) is modified from [vim-terminal-help](https://github.com/skywind3000/vim-terminal-help/blob/master/tools/utils/drop)

- Some features require [neovim-remote](https://github.com/mhinz/neovim-remote)

## License

MIT
