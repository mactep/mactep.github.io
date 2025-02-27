---
categories: neovim
date: "2021-08-10T09:00:00Z"
title: Neovim configuration
---

This post provides the configuration files for a setup in lua and using
Neovim's built-in LSP client and a reasoning around those choices. All the
configuration can be found [here](https://github.com/mactep/dotfiles/tree/nvim_lsp/.config/nvim).
This isn't by any means a guide on how to do things, neither an example of good
code. It just works™.

### `init.lua`
There isn't much to see here, just entry points for configuration files and a
weird command atop. The later just makes `gf` navigate to the configuration
file used as parameter inside `require`. It can safely be ignored.

### `colorscheme.lua`, `mappings.lua` and `settings.lua`
Here are mostly personal stuff. It's worth to note though the alias `map = vim.api.nvim_set_keymap`,
used on other files.

### `plugins.lua`
I manage my plugins with `paq-nvim` and here we can find a bootstrapping for
the plugin manager:
```lua
local install_path = vim.fn.stdpath("data") .. "/site/pack/paqs/opt/paq-nvim"

if vim.fn.empty(vim.fn.glob(install_path)) > 0 then
  vim.cmd("!git clone https://github.com/savq/paq-nvim " .. install_path)
end
```

`vim.cmd[[ PaqClean ]]` and `vim.cmd[[ PaqInstall ]]` cleans up and install
plugins upon initialization.

Lastly we have a routine that requires all files inside `plugins_config`, so we
don't have to do this manually:
```lua
local plugin_dir = vim.fn.stdpath('config')..'/lua/plugins_config'
local p = io.popen('find "'..plugin_dir..'" -type f -printf "%f\n"')
for file in p:lines() do
    file = string.gsub(file, ".lua", "")
    require('plugins_config/'..file)
end
```

### `plugins_config`
Most of the code inside this folder is just configuration needed to make
plugins work and personal preferences. In order to make Neovim start without
errors, the following code is used before calling any plugin:
```lua
local present, PLUGIN = pcall(require, 'PLUGIN')
if not present then
    return
end
```
This just checks if the plugin is installed and runs the setup if so.

Some of the features provided by these configurations are:
- Indentation level guides;
- Highlighting of colors written in the file;
- File explorer;
- Fuzzy finding files, live grep, fuzzy buffer finding and much more fuzzy
  fuzzing;
- Treesitter stuff;
- LSP configuration:
    - Default keymaps;
    - Show diagnostics on cursor over;
    - Show function signature;
    - Enable LSP snippet support;
    - Disable virtual text diagnostic, since it's really annoying;
    - Setup Python and Typescript servers check [lspconfig](https://github.com/neovim/nvim-lspconfig) for more;
- Autocomplete behaviour:
    - `Tab` confirms the first completion suggestion, inserting the remaining
      characters, expanding if it's a snippet, inserting and jump inside
      parenthesis if it's a function and tabbing out. It's far from perfect and
      probably will change in the near future.
    - `<C-j>` and `<C-k>` may also be used to jump around snippet placeholders.
      This is likely to remove this behaviour from `Tab`. Further
      experimentation required.
