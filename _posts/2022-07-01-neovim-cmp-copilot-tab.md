---
layout: post
title: "Neovim nvim-cmp copilot.vim integration using Tab"
date: 2022-07-01 01:00:00 -0300
categories: neovim

---

```lua
cmp.setup({
    ...
	mapping = cmp.mapping.preset.insert({
		["<Tab>"] = cmp.mapping(function(fallback)
			local copilot_keys = vim.fn["copilot#Accept"]()
			if cmp.visible() then
				cmp.confirm({ select = true })
			elseif copilot_keys ~= "" and type(copilot_keys) == "string" then
				vim.api.nvim_feedkeys(copilot_keys, "n", true)
			else
				fallback()
			end
		end, {
			"i",
			"s",
		}),
	}),
    ...
})
```
