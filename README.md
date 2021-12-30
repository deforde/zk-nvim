# zk-nvim
Neovim extension for the [zk](https://github.com/mickael-menu/zk) plain text note-taking assistant.

## Installation

This plugin requires Neovim v0.6.0 or later.

Via [packer.nvim](https://github.com/wbthomason/packer.nvim)
```lua
use("mickael-menu/zk-nvim")
```

Via [vim-plug](https://github.com/junegunn/vim-plug)
```viml
Plug "mickael-menu/zk-nvim"
```

To get the best experience, it's recommended to also install either [Telescope](https://github.com/nvim-telescope/telescope.nvim) or [FZF](https://github.com/junegunn/fzf).

## Setup

> :warning: This plugin will setup and start the LSP server for you, do *not* call `require("lspconfig").zk.setup()`.

```lua
require("zk").setup()
```

**The default configuration**
```lua
require("zk").setup({
  -- can be "telescope", "fzf" or "select" (`vim.ui.select`)
  -- it's recommended to use "telescope" or "fzf"
  picker = "select",

  lsp = {
    -- `config` is passed to `vim.lsp.start_client(config)`
    config = {
      cmd = { "zk", "lsp" },
      name = "zk",
      -- on_attach = ...
      -- etc, see `:h vim.lsp.start_client()`
    },

    -- automatically attach buffers in a zk notebook that match the given filetypes
    auto_attach = {
      enabled = true,
      filetypes = { "markdown" },
    },
  },
})
```

Note that the `setup` function will not add any key mappings for you.
If you want to add key mappings, see the [example mappings](#example-mappings).

### Notebook Directory Discovery
When you run a notebook command, this plugin will look for a notebook in the following places and order:
1. the current buffer path (i.e. the file you are currently editing),
2. the current working directory,
3. the `$ZK_NOTEBOOK_DIR` environment variable.

We recommend you to export the `$ZK_NOTEBOOK_DIR` environment variable, so that a notebook can always be found.

It is worth noting that for some notebook commands you can explicitly specify a notebook by providing a path to any file or directory within the notebook.
An explicitly provided path will always take precedence and override the automatic notebook discovery.
However, this is always optional, and usually not necessary.

## Getting Started

After you have installed the plugin and added the setup code to your config, you are good to go. If you are not familiar with zk, we recommed you to also read the [zk docs](https://github.com/mickael-menu/zk/tree/main/docs).

When using the default config, the zk LSP client will automatically attach itself to buffers inside your notebook and provide capabilities like completion, hover and go-to-definition; see https://github.com/mickael-menu/zk/issues/22 for a full list of what is supported.

Try out different [commands](#built-in-commands) such as `:ZkNotes` or `:ZkNew`, see what they can do, and learn as you go.

## Built-in Commands

**Via VimL**
```vim
" Indexes the notebook
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zkindex
:ZkIndex [{options}]
```

```vim
" Creates and edits a new note
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
:ZkNew [{options}]
```

```vim
" Creates a new note and uses the last visual selection as the title while replacing the selection with a link to the new note
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
:'<,'>ZkNewFromTitleSelection [{options}]
```

```vim
" Creates a new note and uses the last visual selection as the content while replacing the selection with a link to the new note
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
:'<,'>ZkNewFromContentSelection [{options}]
```

```vim
" cd into the notebook root
" params
"   (optional) additional options
:ZkCd [{options}]
```

```vim
" Opens a notes picker
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
:ZkNotes [{options}]
```

```vim
" Opens a notes picker
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
:ZkBacklinks [{options}]
```

```vim
" Opens a notes picker
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
:ZkLinks [{options}]
```

```vim
" Opens a notes picker, filters for notes that match the text in the last visual selection
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
:'<,'>ZkMatch [{options}]
```

```vim
" Opens a notes picker
" params
"   (optional) additional options, see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zktaglist
:ZkTags [{options}]
```

The `options` parameter can be any valid *Lua* expression that evaluates to a table.
For a list of available options, refer to the [zk docs](https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#custom-commands).
In addition, `options.notebook_path` can be used to explicitly specify a notebook by providing a path to any file or directory within the notebook; see [Notebook Directory Discovery](#notebook-directory-discovery).

*Examples:*
```vim
:ZkNew { dir = "daily", date = "yesterday" }
:ZkNotes { createdAfter = "3 days ago", tags = { "work" } }
:'<,'>ZkNewFromTitleSelection " this will use your last visual mode selection. Note that you *must* call this command with the '<,'> range.
:ZkCd
```

---
**Via Lua**
```lua
---Indexes the notebook
--
---@param options table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zkindex
require("zk.commands").index(options)
```
```lua
---Creates and edits a new note
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
require("zk.commands").new(options)
```
```lua
---Creates a new note and uses the last visual selection as the title while replacing the selection with a link to the new note
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
require("zk.commands").new_from_title_selection(options)
```
```lua
---Creates a new note and uses the last visual selection as the content while replacing the selection with a link to the new note
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
require("zk.commands").new_from_content_selection(options)
```
```lua
---cd into the notebook root
--
---@param options? table additional options
require("zk.commands").cd(options)
```
```lua
---Opens a notes picker
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
require("zk.commands").notes(options)
```
```lua
---Opens a notes picker
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
require("zk.commands").backlinks(options)
```
```lua
---Opens a notes picker
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
require("zk.commands").links(options)
```
```lua
---Opens a notes picker, filters for notes that match the text in the last visual selection
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
require("zk.commands").match(options)
```
```lua
---Opens a notes picker
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zktaglist
require("zk.commands").tags(options)
```

For a list of available options, refer to the [zk docs](https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#custom-commands).
In addition, `options.notebook_path` can be used to explicitly specify a notebook by providing a path to any file or directory within the notebook; see [Notebook Directory Discovery](#notebook-directory-discovery).

*Examples:*
```lua
require("zk.commands").new({ dir = "daily" })
require("zk.commands").notes({ createdAfter = "3 days ago", tags = { "work" } })
require("zk.commands").new_from_title_selection() -- this will use your last visual mode selection
```

## Custom Commands

You are free to add your own commands to the `require("zk.commands")` module.
To do this, simply add your function like this:
```lua
require("zk.commands").foo_bar = function()
  -- Do something
end
vim.cmd("command! ZkFooBar lua require('zk.commands').foo_bar()")
```
For convenience, you can also add a matching vim user command all at once like this:
```lua
require("zk.commands").foo_bar = {
  command = "ZkFooBar",
  fn = function(options)
    -- Do something
  end,
}
```
This would add a `:ZkFooBar [{options}]` command which will call `fn` with the `options` Lua table as an argument.

You will probably want to use the [high-level API](#high-level-api) inside of your custom commands.
For example, the `zk.edit` function in the following examples is from the high-level API.

*Example 1:*

Let us add a custom `:ZkOrphans` command that will list all notes that are orphans, i.e. not referenced by any other note.

```lua
local zk = require("zk")
local commands = require("zk.commands")

zk.setup({
  -- your config ...
})

commands.orphans = {
  command = "ZkOrphans",
  fn = function(options)
    options = vim.tbl_extend("force", { orphan = true }, options or {})
    zk.edit(options, picker_options)
  end,
}
```
The given code adds
1. a `:ZkOrphans [{options}]` vim user command, and
2. a Lua function `require("zk.commands").orphans`.

Same as the builtin commands, the new command also accepts an options Lua table as an argument.
We can execute it like this `:ZkOrphans { tags = "work" }` for example.

*Example 2:*

Chances are that this will not be our only custom command following this pattern.
So let's also add a `:ZkRecents` command and make the pattern a bit more reusable.

```lua
local zk = require("zk")
local commands = require("zk.commands")

zk.setup({
  -- your config ...
})

local function make_edit_cmd(name, defaults, picker_options)
  return {
    command = name,
    fn = function(options)
      options = vim.tbl_extend("force", defaults, options or {})
      zk.edit(options, picker_options)
    end,
  }
end

commands.orphans = make_edit_cmd("ZkOrphans", { orphan = true }, { title = "Zk Orphans" })
commands.recents = make_edit_cmd("ZkRecents", { createdAfter = "2 weeks ago" }, { title = "Zk Recents" })
```

## High-level API

The high-level API is inspired by the commands provided by the zk CLI tool; see `zk --help`.
It's mainly used for the implementation of built-in and custom commands.

```lua
---Cd into the notebook root
--
---@param options? table
require("zk").cd(options)
```

```lua
---Creates and edits a new note
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
require("zk").new(options)
```

```lua
---Indexes the notebook
--
---@param options? table additional options
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zkindex
require("zk").index(options)
```

```lua
---Opens a notes picker, and calls the callback with the selection
--
---@param options? table additional options
---@param picker_options? table options for the picker
---@param cb function
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
---@see zk.ui.pick_notes
require("zk").pick_notes(options, picker_options, cb)
```

```lua
---Opens a tags picker, and calls the callback with the selection
--
---@param options? table additional options
---@param picker_options? table options for the picker
---@param cb function
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zktaglist
---@see zk.ui.pick_tags
require("zk").pick_tags(options, picker_options, cb)
```

```lua
---Opens a notes picker, and edits the selected notes
--
---@param options? table additional options
---@param picker_options? table options for the picker
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
---@see zk.ui.pick_notes
require("zk").edit(options, picker_options)
```

```lua
---Opens a tags picker, then opens a notes picker for the selected tags, and finally edits the selected notes
--
---@param options? table additional options
---@param picker_options? table options for the picker
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zktaglist
---@see zk.ui.pick_tags
require("zk").edit_from_tags(options, picker_options)
```

## API

The functions in the API module give you maximum flexibility and provide only a thin Lua friendly layer around zk's API.
You can use it to write your own specialized functions for interacting with zk.

```lua
---@param path? string path to explicitly specify the notebook
---@param options? table additional options
---@param cb function callback function
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zkindex
require("zk").api.index(path, options, function(stats)
  -- do something with the stats
end)
```

```lua
---@param path? string path to explicitly specify the notebook
---@param options? table additional options
---@param cb function callback function
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zknew
require("zk").api.new(path, options, function(res)
  file_path = res.path
  -- do something with the new file path
end)
```

```lua
---@param path? string path to explicitly specify the notebook
---@param options table additional options
---@param cb function callback function
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zklist
require("zk").api.list(path, options, function(notes)
  -- do something with the notes
end)
```

```lua
---@param path? string path to explicitly specify the notebook
---@param options? table additional options
---@param cb function callback function
---@see https://github.com/mickael-menu/zk/blob/main/docs/editors-integration.md#zktaglist
require("zk").api.tag.list(path, options, function(tags)
  -- do something with the tags
end)
```

## Pickers

Used by the [high-level API](#high-level-api) to display the results of the [API](#api).

```lua
---Opens a notes picker
--
---@param notes list
---@param options? table containing {picker}, {title}, {multi_select} keys
---@param cb function
require("zk.ui").pick_notes(notes, options, cb)
```

```lua
---Opens a tags picker
--
---@param tags list
---@param options? table containing {picker}, {title}, {multi_select} keys
---@param cb function
require("zk.ui").pick_tags(tags, options, cb)
```

```lua
---To be used in zk.api.list as the `selection` in the additional options table
--
---@param options table the same options that are use for pick_notes
---@return table api selection
require("zk.ui").get_pick_notes_list_api_selection(options)
```

## Example Mappings
```lua
vim.api.nvim_set_keymap("n", "<Leader>zc", "<cmd>lua require('zk.commands').new()<CR>", { noremap = true })
vim.api.nvim_set_keymap("x", "<Leader>zc", "<esc><cmd>lua require('zk.commands').new_from_title_selection()<CR>", { noremap = true })
vim.api.nvim_set_keymap("n", "<Leader>zn", "<cmd>lua require('zk.commands').notes()<CR>", { noremap = true })
vim.api.nvim_set_keymap("n", "<Leader>zb", "<cmd>lua require('zk.commands').backlinks()<CR>", { noremap = true })
vim.api.nvim_set_keymap("n", "<Leader>zl", "<cmd>lua require('zk.commands').links()<CR>", { noremap = true })
vim.api.nvim_set_keymap("n", "<Leader>zt", "<cmd>lua require('zk.commands').tags()<CR>", { noremap = true })
```

# Miscellaneous

## Syntax Highlighting Tips

These code snippets only work if you use Neovim's built-in Markdown syntax highlighting.

*Proper syntax highlighting for Wikilinks.* [[This is a wiki link]].
```vim
autocmd Filetype markdown syn region markdownWikiLink matchgroup=markdownLinkDelimiter start="\[\[" end="\]\]" contains=markdownUrl keepend oneline concealends
```

*Conceal support for normal Markdown links.* Overwrite the syntax regions like so
```vim
autocmd Filetype markdown syn region markdownLinkText matchgroup=markdownLinkTextDelimiter start="!\=\[\%(\%(\_[^][]\|\[\_[^][]*\]\)*]\%( \=[[(]\)\)\@=" end="\]\%( \=[[(]\)\@=" nextgroup=markdownLink,markdownId skipwhite contains=@markdownInline,markdownLineStart concealends
autocmd Filetype markdown syn region markdownLink matchgroup=markdownLinkDelimiter start="(" end=")" contains=markdownUrl keepend contained conceal
```

## nvim-lsp-installer
> Not recommeded, instead install the [zk](https://github.com/mickael-menu/zk) CLI tool and make it available in your `$PATH`.

If you insist to use nvim-lsp-installer for zk, the following code snippet should guide you on how to setup the zk server when installed via nvim-lsp-installer.

```lua
require("nvim-lsp-installer").on_server_ready(function(server)
  local opts = {
    -- customize your options as usual
    --
    -- on_attach = ...
    -- etc, see `:h vim.lsp.start_client()`
  }
  if server.name == "zk" then
    require("zk").setup({
      lsp = {
        config = vim.tbl_extend("force", server:get_default_options(), opts),
      },
    })
  else
    server:setup(opts)
  end
end)
```

## Telescope Plugin
> Not recommeded, instead just use the [:ZkNotes and :ZkTags commands](#built-in-commands).

It's possible (but unnecessary) to also load the notes / tags pickers as a telescope plugin.

```lua
require("telescope").load_extension("zk")
```

```vim
:Telescope zk notes
:Telescope zk tags
```
