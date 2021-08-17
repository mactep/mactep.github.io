---
layout: post
title: "Using Angular language server on older projects with Neovim's LSP
client"
date: 2021-08-10 09:00:00 -0300
categories: angular, neovim

---

If you're using [https://github.com/neovim/nvim-lspconfig](nvim-lspconfig's)
Angular server in an older project, in my case one with version six, you're
probably facing many issues and having a bad experience. That may be due to a
missing parameter on the server command.

To fix this, try setting up the server like this:

```lua
local languageServerPath = vim.fn.stdpath("config").."/languageserver"
local cmd = {"ngserver", "--stdio", "--tsProbeLocations", languageServerPath , "--ngProbeLocations", languageServerPath, "--viewEngine"}

lspconfig.angularls.setup{
    on_attach = on_attach,
    capabilities = capabilities,
    cmd = cmd,
    on_new_config = function(new_config, new_root_dir)
        new_config.cmd = cmd
    end,
}
```

The key here is the `--viewEngine` argument, that maker the language service
use it's legacy configuration, rather than the newer, known as Angular Ivy.

Mind that the `languageServerPath` variable is the path to the location where
`@angular/language-service` and `typescript` are installed.

Waiting for [this issue](https://github.com/neovim/nvim-lspconfig/issues/1155)
to provide further information on the solution.
