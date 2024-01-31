Readme.md

[![Lua](https://img.shields.io/badge/lua-%232C2D72.svg?style=for-the-badge&logo=lua&logoColor=white)](https://www.lua.org/)
[![Neovim](https://img.shields.io/badge/NeoVim-%2357A143.svg?&style=for-the-badge&logo=neovim&logoColor=white)](https://neovim.io/)
[![Debian](https://img.shields.io/badge/Debian-D70A53?style=for-the-badge&logo=debian&logoColor=white)](https://www.debian.org/)

Find me:

[![LinkedIn](https://img.shields.io/badge/linkedin-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/ajay110/)
[![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)](https://github.com/underscoore)
[![X](https://img.shields.io/badge/X-%23000000.svg?style=for-the-badge&logo=X&logoColor=white)](https://twitter.com/ajayonx)

# Neovim Configuration Guide

This guide will walk you through the process of configuring Neovim, a highly extensible text editor that is compatible with Vim, but with improved performance and additional features. This documentation is only for Linux Debian destribution only but may/may not work for other destributions.

## Prerequisites

Before you begin configuring Neovim, ensure that you have Neovim installed on your system. You can download and install Neovim from its [official website](https://neovim.io/).

> Install [ripgrep](https://github.com/BurntSushi/ripgrep)

```shell
sudo apt install ripgrep
```

> Install Nodejs (for live servers, auto complete & auto sugessation ). You can download latest version of Nodejs from its [official website](https://nodejs.org/en)

```shell
sudo apt install nodejs
```

## Basic Configuration

### Getting Start

**Open Neovim:** Launch Neovim by typing `'nvim'` in your terminal.

**Access the Configuration File:** Neovim's configuration file is located at `~/.config/nvim/init.vim.` If this file does not exist, you can create it.

**Configure Plugins:** Neovim supports plugin management through various plugin managers like `vim-plug, Vundle, Packer and dein.vim`. Choose a plugin manager and follow its installation instructions. Personally I am using Packer

**Edit the Configuration File:** Open `init.vim` in your preferred text editor (oviously nvim). This file will contain contains Vimscript and Neovim-specific configuration options.

**Basic Configuration:** You can start with basic configurations such as setting the color scheme, enabling line numbers, and defining keyboard mappings. Here's a basic example:

```lua
"Example basic configuration
syntax enable
set number
set relativenumber
set tabstop=4
set shiftwidth=4
colorscheme desert

```

**Plugin Configuration:** If you're using a plugin manager, add plugin configurations to `init.vim`. Refer to the documentation of each plugin for specific configuration options.

```lua
" Example plugin configuration using vim-plug
call plug#begin('~/.vim/plugged')
Plug 'vim-airline/vim-airline'
Plug 'tpope/vim-fugitive'
" Add more plugins as needed
call plug#end()

```

**Save and Exit:** After making changes, save the `init.vim` file and exit Neovim.

**Install Plugins:** Launch Neovim again and run `:PackerSync` (if using Packer Manager) or equivalent command for your plugin manager to install configured plugins.

**Restart Neovim:** Close and reopen Neovim to apply the changes.

AND BOOOM 💥 You set with very basic vim configuration!!

## Creasy Stuff begins here!!

### Directory Structure

Get all the configurations below

```bash
.config
├── nvim
│   ├── after
│   │   └── plugin
│   │       ├── colors.lua
│   │       ├── harppon.lua
│   │       ├── lsp.lua
│   │       ├── telescope.lua
│   │       ├── treesitter.lua
│   │       └── undotree.lua
│   ├── init.lua
│   └── lua
│       └── ajay
│           ├── init.lua
│           ├── packer.lua
│           ├── remap.lua
│           └── set.lua
└── README.md
```

This file contains the initial settings for vim. It specifies `nvim/init.lua` vim searches for this file first. You can add as many configurations in it.

```lua
require("ajay")
```

Inside `ajay` we have several files `init.lua, packer.lua, remap.lua, set.lua`. Let's go through one by one

```lua
--ajay/init.lua

require("ajay.remap")
require("ajay.set")


local augroup = vim.api.nvim_create_augroup
local Group = augroup('TheGroup', {})

local autocmd = vim.api.nvim_create_autocmd
local yank_group = augroup('HighlightYank', {})

function R(name)
    require("plenary.reload").reload_module(name)
end

autocmd('TextYankPost', {
    group = yank_group,
    pattern = '*',
    callback = function()
        vim.highlight.on_yank({
            higroup = 'IncSearch',
            timeout = 40,
        })
    end,
})

autocmd({"BufWritePre"}, {
    group = Group,
    pattern = "*",
    command = [[%s/\s\+$//e]],
})

vim.g.netrw_browse_split = 0
vim.g.netrw_banner = 0
vim.g.netrw_winsize = 25
```

We are calling vim api's forconfigureation:

- `vim.api.nvim_create_augroup`: for creating new group and providing group name
- `vim.api.nvim_create_autocmd`: create Autocommans in Neovim
- `vim.highlight.on_yank`: is used to highlight when performing inSearch
- `vim.g.netrw_browse_split`: option in Vim is significant when using the built-in file browser, **NetRW**. It determines how files are opened when browsing directories.
- `vim.g.netrw_banner`: option in Vim controls the display of the banner at the top of the NetRW file browser
- `vim.g.netrw_winsize`: option in Vim allows you to control the initial size of windows when using the built-in file browser

The file named "packer.lua" typically indicates the configuration file for Packer, a popular plugin manager for Neovim and Vim. Packer.lua allows users to manage their plugins in a Lua-based configuration file rather than the traditional Vimscript.

Here's a breakdown of its usage:

1. **Plugin Management**: Packer.lua is used to declare and manage plugins within Neovim or Vim. Users specify the plugins they want to install, update, or remove in this file.

2. **Configuration**: Users can configure various aspects of their plugins using Lua syntax within the Packer.lua file. This includes specifying plugin options, mappings, hooks, and more.

3. **Dependency Management**: Packer.lua handles dependencies between plugins. It ensures that all required plugins are installed and loaded correctly, resolving dependencies automatically based on the configuration specified in the file.

4. **Installation**: Packer.lua simplifies the installation process of plugins. Users define the plugins they want to install, and Packer.lua handles the downloading and setup of those plugins.

5. **Updating and Removal**: Packer.lua provides commands and functions to update and remove plugins. This allows users to keep their plugin setup up-to-date and clean.

```lua
-- ajay/packer.lue

-- This file can be loaded by calling `lua require('plugins')` from your init.vim

-- Only required if you have packer configured as `opt`
vim.cmd [[packadd packer.nvim]]

return require('packer').startup(function(use)
	-- Packer can manage itself
	use 'wbthomason/packer.nvim'

  use {
	  'nvim-telescope/telescope.nvim', tag = '0.1.5',
	  requires = { {'nvim-lua/plenary.nvim'} }
  }

  use({
	  'rose-pine/neovim',
	  as = 'rose-pine',
	  config = function()
		  vim.cmd('colorscheme rose-pine')
	  end
  })

  use('nvim-treesitter/nvim-treesitter', {run = ':TSUpdate'});
  use('nvim-treesitter/playground')
  use('thePrimeagen/harpoon')
  use('mbbill/undotree')
  use('tpope/vim-fugitive')

  use {
    "williamboman/nvim-lsp-installer",
    "neovim/nvim-lspconfig",
  }
  use('davidgranstrom/nvim-markdown-preview')

  use{
  'VonHeikemen/lsp-zero.nvim',
  branch = 'v3.x',
  requires = {
  	{'neovim/nvim-lspconfig'},
		  {'williamboman/mason.nvim'},
		  {'williamboman/mason-lspconfig.nvim'},

		  -- Autocompletion
		  {'hrsh7th/nvim-cmp'},
		  {'hrsh7th/cmp-buffer'},
		  {'hrsh7th/cmp-path'},
		  {'saadparwaiz1/cmp_luasnip'},
		  {'hrsh7th/cmp-nvim-lsp'},
		  {'hrsh7th/cmp-nvim-lua'},

		  -- Snippets
		  {'L3MON4D3/LuaSnip'},
		  {'rafamadriz/friendly-snippets'},
	  }

}
end)
```

The `remap.lua` file in Neovim is typically used for defining custom key mappings or remappings in Lua. Neovim allows users to define key mappings in various ways, including through Vimscript and Lua.

Here's a brief overview of how the `remap.lua` file might be used:

1. **Defining Custom Key Mappings**: The primary purpose of the `remap.lua` file is to define custom key mappings. These mappings allow users to customize their editing experience by assigning specific actions or commands to key combinations that are convenient for them.

2. **Lua Configuration in Neovim**: Neovim introduced Lua as a first-class configuration language alongside Vimscript. Lua provides more flexibility and power for configuring Neovim, including defining custom key mappings.

3. **Separation of Concerns**: Keeping key mappings in a separate Lua file like `remap.lua` helps keep the configuration organized and makes it easier to manage and maintain, especially for larger configurations.

4. **Readability and Modularity**: Lua allows for more expressive and readable configuration compared to Vimscript. By using Lua, users can write cleaner and more modular code for defining key mappings and other configuration settings.

Here's a simple example of how the `remap.lua` file might look:

```lua
-- ajay/remap.lua

-- Define key mappings
vim.g.mapleader = " "
vim.keymap.set("n", "<leader>pv", vim.cmd.Ex)


vim.g.mapleader = " "
vim.keymap.set("n", "<leader>pv", vim.cmd.Ex)

vim.keymap.set("v", "J", ":m '>+1<CR>gv=gv")
vim.keymap.set("v", "K", ":m '<-2<CR>gv=gv")

vim.keymap.set("n", "J", "mzJ`z")
vim.keymap.set("n", "<C-d>", "<C-d>zz")
vim.keymap.set("n", "<C-u>", "<C-u>zz")
vim.keymap.set("n", "n", "nzzzv")
vim.keymap.set("n", "N", "Nzzzv")

vim.keymap.set("n", "<leader>vwm", function()
    require("vim-with-me").StartVimWithMe()
end)
vim.keymap.set("n", "<leader>svwm", function()
    require("vim-with-me").StopVimWithMe()
end)

-- greatest remap ever
vim.keymap.set("x", "<leader>p", [["_dP]])

-- next greatest remap ever : asbjornHaland
vim.keymap.set({"n", "v"}, "<leader>y", [["+y]])
vim.keymap.set("n", "<leader>Y", [["+Y]])

vim.keymap.set({"n", "v"}, "<leader>d", [["_d]])

-- This is going to get me cancelled
vim.keymap.set("i", "<C-c>", "<Esc>")

vim.keymap.set("n", "Q", "<nop>")
vim.keymap.set("n", "<C-f>", "<cmd>silent !tmux neww tmux-sessionizer<CR>")
vim.keymap.set("n", "<leader>f", vim.lsp.buf.format)

vim.keymap.set("n", "<C-k>", "<cmd>cnext<CR>zz")
vim.keymap.set("n", "<C-j>", "<cmd>cprev<CR>zz")
vim.keymap.set("n", "<leader>k", "<cmd>lnext<CR>zz")
vim.keymap.set("n", "<leader>j", "<cmd>lprev<CR>zz")

vim.keymap.set("n", "<leader>s", [[:%s/\<<C-r><C-w>\>/<C-r><C-w>/gI<Left><Left><Left>]])
vim.keymap.set("n", "<leader>x", "<cmd>!chmod +x %<CR>", { silent = true })

vim.keymap.set("n", "<leader>vpp", "<cmd>e ~/.dotfiles/nvim/.config/nvim/lua/theprimeagen/packer.lua<CR>");
vim.keymap.set("n", "<leader>mr", "<cmd>CellularAutomaton make_it_rain<CR>");

vim.keymap.set("n", "<leader><leader>", function()
    vim.cmd("so")
end)
```

In this example, `<leader>f` and `<leader>g` are mapped to Telescope commands for finding files and performing live grep searches, respectively. The `noremap` and `silent` options are also specified to control how the mappings are applied.

The `set.lua` file in NeoVim is typically used to configure and customize settings using Lua scripting. NeoVim introduced Lua as an additional scripting language alongside Vimscript to provide users with more flexibility and power in configuring their editor.

The `set.lua` file, as its name suggests, is often used to set various options, key mappings, plugins, and other customizations in NeoVim using Lua syntax. It's a way to organize and centralize configuration settings in a separate file for easier management and maintenance.

Here are some common uses of `set.lua` in NeoVim:

1. **Setting Options**: You can set various NeoVim options such as `tabstop`, `shiftwidth`, `number`, `relativenumber`, `autoindent`, etc., using Lua syntax.

2. **Defining Key Mappings**: You can define key mappings and keybindings using Lua functions. For example, mapping keys to execute certain commands or functions.

3. **Plugin Configuration**: Many NeoVim users manage their plugins using Lua-based plugin managers like `packer.nvim` or `nvim-compe`. Plugin configurations and settings can be organized within `set.lua`.

4. **Custom Functions and Commands**: You can define custom Lua functions and commands to extend NeoVim's functionality.

Here's a basic example of how a `set.lua` file might look:

```lua
-- ajay/set.lua

-- Set options
vim.opt.guicursor = ""

vim.opt.nu = true
vim.opt.relativenumber = true

vim.opt.tabstop = 4
vim.opt.softtabstop = 4
vim.opt.shiftwidth = 4
vim.opt.expandtab = true

vim.opt.smartindent = true

vim.opt.wrap = false

vim.opt.swapfile = false
vim.opt.backup = false
vim.opt.undodir = os.getenv("HOME") .. "/.vim/undodir"
vim.opt.undofile = true

vim.opt.hlsearch = false
vim.opt.incsearch = true

vim.opt.termguicolors = true

vim.opt.scrolloff = 8
vim.opt.signcolumn = "yes"
vim.opt.isfname:append("@-@")

vim.opt.updatetime = 50

vim.opt.colorcolumn = "80"
```

In this example, we're setting various options using `vim.opt`, defining a key mapping using `vim.api.nvim_set_keymap`, and configuring plugins using `packer.nvim`.

The `set.lua` file provides a convenient way to organize and manage NeoVim configurations using Lua scripting, making it easier to maintain and extend your editor setup.

## Plugin Files

In NeoVim, plugin files refer to files that extend or enhance the functionality of the editor by providing additional features, integrations, or customizations. here are the configurations of plugin file.

```lua
-- colors.lua

require('rose-pine').setup({
    disable_background = true
})

function ColorMyPencils(color)
	color = color or "rose-pine"
	vim.cmd.colorscheme(color)

	vim.api.nvim_set_hl(0, "Normal", { bg = "none" })
	vim.api.nvim_set_hl(0, "NormalFloat", { bg = "none" })

end

ColorMyPencils()
```

```lua
-- fugitive.lua

vim.keymap.set("n", "<leader>gs", vim.cmd.Git)
```

```lua
-- harpoon.lua

local mark = require("harpoon.mark")
local ui = require("harpoon.ui")

vim.keymap.set("n", "<leader>a", mark.add_file)
vim.keymap.set("n", "<C-e>", ui.toggle_quick_menu)

vim.keymap.set("n", "<C-h>", function() ui.nav_file(1) end)
vim.keymap.set("n", "<C-t>", function() ui.nav_file(2) end)
vim.keymap.set("n", "<C-n>", function() ui.nav_file(3) end)
vim.keymap.set("n", "<C-s>", function() ui.nav_file(4) end)
```

```lua
-- lsp.lua

local lsp = require("lsp-zero")
lsp.preset('recommended')

local cmp = require('cmp')
local cmp_select = {behavior = cmp.SelectBehavior.Select}
local cmp_mappings = lsp.defaults.cmp_mappings({
  ['<C-p>'] = cmp.mapping.select_prev_item(cmp_select),
  ['<C-n>'] = cmp.mapping.select_next_item(cmp_select),
  ['<C-y>'] = cmp.mapping.confirm({ select = true }),
  ["<C-Space>"] = cmp.mapping.complete(),
})


lsp.set_preferences({
    suggest_lsp_servers = false,
    sign_icons = {
        error = 'E',
        warn = 'W',
        hint = 'H',
        info = 'I',
    }
})

-- Setup language servers.
local lspconfig = require('lspconfig')
lspconfig.pyright.setup {}
lspconfig.tsserver.setup {}


-- Global mappings.
-- See `:help vim.diagnostic.*` for documentation on any of the below functions
vim.keymap.set('n', '<space>e', vim.diagnostic.open_float)
vim.keymap.set('n', '[d', vim.diagnostic.goto_prev)
vim.keymap.set('n', ']d', vim.diagnostic.goto_next)
vim.keymap.set('n', '<space>q', vim.diagnostic.setloclist)


lsp.on_attach(function(client, bufnr)
  local opts = {buffer = bufnr, remap = false}

  vim.keymap.set("n", "gd", function() vim.lsp.buf.definition() end, opts)
  vim.keymap.set("n", "K", function() vim.lsp.buf.hover() end, opts)
  vim.keymap.set("n", "<leader>vws", function() vim.lsp.buf.workspace_symbol() end, opts)
  vim.keymap.set("n", "<leader>vd", function() vim.diagnostic.open_float() end, opts)
  vim.keymap.set("n", "[d", function() vim.diagnostic.goto_next() end, opts)
  vim.keymap.set("n", "]d", function() vim.diagnostic.goto_prev() end, opts)
  vim.keymap.set("n", "<leader>vca", function() vim.lsp.buf.code_action() end, opts)
  vim.keymap.set("n", "<leader>vrr", function() vim.lsp.buf.references() end, opts)
  vim.keymap.set("n", "<leader>vrn", function() vim.lsp.buf.rename() end, opts)
  vim.keymap.set("i", "<C-h>", function() vim.lsp.buf.signature_help() end, opts)
end)

lsp.setup()

vim.diagnostic.config({
    virtual_text = true
})
```

```lua
--telescope.lua

local builtin = require('telescope.builtin')
vim.keymap.set('n', '<leader>pf', builtin.find_files, {})
vim.keymap.set('n', '<C-p>', builtin.git_files, {})
vim.keymap.set('n', '<leader>ps', function()
	builtin.grep_string({ search = vim.fn.input("Grep > ") })
end)
vim.keymap.set('n', '<leader>vh', builtin.help_tags, {})
```

```lua
-- treesitter.lua

require'nvim-treesitter.configs'.setup {
  -- A list of parser names, or "all" (the five listed parsers should always be installed)
  ensure_installed = { "javascript", "typescript", "rust", "python", "html", "c", "lua", "vim", "vimdoc", "query" },

  -- Install parsers synchronously (only applied to `ensure_installed`)
  sync_install = false,

  -- Automatically install missing parsers when entering buffer
  -- Recommendation: set to false if you don't have `tree-sitter` CLI installed locally
  auto_install = true,

  highlight = {
    enable = true,

    -- Setting this to true will run `:h syntax` and tree-sitter at the same time.
    -- Set this to `true` if you depend on 'syntax' being enabled (like for indentation).
    -- Using this option may slow down your editor, and you may see some duplicate highlights.
    -- Instead of true it can also be a list of languages
    additional_vim_regex_highlighting = false,
  },
}
```

```lua
-- undotreee.lua

vim.keymap.set("n", "<leader>u", vim.cmd.UndotreeToggle)
```

You can customize your own configuration and find out more details about it. 🧑‍🏫

### Plugins:

- [telescope](https://github.com/nvim-telescope/telescope.nvim)
- [plenary](https://github.com/nvim-lua/plenary.nvim)
- [rose-pine](https://github.com/rose-pine/neovim)
- [treesitter](https://github.com/nvim-treesitter/nvim-treesitter)
- [undotree](https://github.com/mbbill/undotree)
- [vim-fugitive](https://github.com/tpope/vim-fugitive)
- [nvim-lsp-installer](https://github.com/williamboman/nvim-lsp-installer)
- [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig)
- [nvim-markdown-preview](https://github.com/iamcco/markdown-preview.nvim)
- [lsp-zero](https://github.com/VonHeikemen/lsp-zero.nvim)
- [mason](https://github.com/williamboman/mason.nvim)
- [mason-lspconfig](https://github.com/williamboman/mason-lspconfig.nvim)
- [nvim-cmp](https://github.com/hrsh7th/nvim-cmp)
- [cmp-buffer](https://github.com/hrsh7th/cmp-buffer)
- [cmp-path](https://github.com/hrsh7th/cmp-path)
- [cmp_luasnip](https://github.com/saadparwaiz1/cmp_luasnip)
- [LuaSnip](https://github.com/L3MON4D3/LuaSnip)
- [friendly-snippets](https://github.com/rafamadriz/friendly-snippets)

Thanks ❣️ to [ThePrimeagen | Github](https://github.com/ThePrimeagen) || [ThePrimeagen | Twitter](https://x.com/ThePrimeagen?s=20) for making [video](https://www.youtube.com/watch?v=w7i4amO_zaE) on nvim configuraion

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/underscoore/nvim-config.github.io/blob/main/LICENSE)

[MD Badges Help! ](https://github.com/Ileriayo/markdown-badges/blob/master/README.md#tips) | [MD License Help!](https://gist.github.com/lukas-h/2a5d00690736b4c3a7ba)

---Thanks---
