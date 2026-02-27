# Agent Guide for kickstart.nvim

This document provides essential information for AI coding agents working in this Neovim configuration repository.

## Repository Overview

This is a **kickstart.nvim** configuration - a minimal, well-documented Neovim setup designed as a starting point rather than a full distribution. The configuration is primarily contained in a single `init.lua` file with optional modular plugins in `lua/kickstart/plugins/` and `lua/custom/plugins/`.

## Project Structure

```
.
├── init.lua                    # Main configuration file (single-file design)
├── lua/
│   ├── custom/
│   │   └── plugins/           # User-added plugins
│   └── kickstart/
│       ├── health.lua         # Health check utilities
│       └── plugins/           # Optional kickstart plugins (debug, autopairs, etc.)
├── .stylua.toml               # Lua formatting configuration
├── lazy-lock.json             # Plugin version lockfile (managed by lazy.nvim)
└── README.md                  # Installation and usage instructions
```

## Build, Lint, and Test Commands

### Formatting

**Format Lua files:**
```bash
stylua .
```

**Format configuration (settings in `.stylua.toml`):**
- Column width: 160
- Indent: 2 spaces
- Quote style: Auto-prefer single quotes
- Call parentheses: None (Lua style)
- Line endings: Unix

### Health Checks

**Check Neovim installation and dependencies:**
```bash
nvim +checkhealth
```

**Check kickstart-specific health:**
```bash
nvim +'checkhealth kickstart'
```

### Plugin Management

**Update plugins:**
```bash
nvim +'Lazy update' +qa
```

**Install/sync plugins:**
```bash
nvim +'Lazy sync' +qa
```

**Check plugin status:**
```bash
nvim +'Lazy' +qa
```

### Testing

This repository does not have automated tests. Changes should be verified by:
1. Running `:checkhealth` to ensure no configuration errors
2. Testing the configuration in Neovim manually
3. Verifying LSP, completion, and other features work as expected

## Code Style Guidelines

### Language

**Primary Language:** Lua (100%)
- Neovim configuration language
- All files use Lua for configuration and plugins

### File Organization

**Single-file preference:** The main configuration is intentionally kept in `init.lua` as a teaching tool. This is by design - modularization is left to users.

**Plugin files:** Return a table or array of plugin specifications following lazy.nvim conventions.

### Lua Style Guide

#### Indentation and Spacing
- Use 2 spaces for indentation (never tabs)
- Maximum line width: 160 characters
- Unix line endings (LF)

#### Quotes
- Auto-prefer single quotes: `'string'` over `"string"`
- Use double quotes when string contains single quotes

#### Function Calls
- Omit parentheses when calling with a single string or table literal:
  ```lua
  require 'telescope'
  vim.keymap.set('n', '<Esc>', '<cmd>nohlsearch<CR>')
  ```

#### Comments
- Use `--` for single-line comments
- Use `--[[  ]]` for multi-line comments
- Document sections with clear comment headers
- Add inline documentation for non-obvious code

#### Variables and Naming
- Use `snake_case` for local variables and functions: `local status_line`, `check_version`
- Use `PascalCase` for plugin names when assigned: `local Telescope`
- Descriptive names over abbreviations

#### Tables
- Use trailing commas in multi-line table definitions
- Align table values for readability when appropriate
- Use Lua's flexible table syntax:
  ```lua
  { 'plugin/name', opts = {} }
  ```

#### Type Annotations
- Use LSP annotations where helpful:
  ```lua
  ---@type vim.Option
  local rtp = vim.opt.rtp
  ```
- Include `@param`, `@return` annotations for complex functions

#### Plugin Configuration Structure
- Follow lazy.nvim plugin specification format
- Use `opts = {}` for simple setups
- Use `config = function() end` for complex configurations
- Specify dependencies explicitly
- Use `event`, `ft`, `keys` for lazy loading

### Error Handling

- Use `pcall()` for operations that might fail gracefully:
  ```lua
  pcall(require('telescope').load_extension, 'fzf')
  ```
- Provide clear error messages with `error()` for critical failures
- Use `vim.health` functions in health check modules

### Vim API Usage

**Prefer modern APIs:**
- Use `vim.keymap.set()` over `vim.api.nvim_set_keymap()`
- Use `vim.o`, `vim.opt`, `vim.g` for options
- Use `vim.uv or vim.loop` for compatibility with different Neovim versions

**Autocommands:**
```lua
vim.api.nvim_create_autocmd('Event', {
  group = vim.api.nvim_create_augroup('group-name', { clear = true }),
  callback = function()
    -- code
  end,
})
```

### LSP Configuration

- Use `mason` for LSP server installation
- Configure capabilities from completion plugin (blink.cmp)
- Set up keymaps in LspAttach autocmd
- Use descriptive keymap descriptions

### Documentation

- Keep comments up-to-date with code changes
- Document why, not what (code should be self-explanatory)
- Include `:help` references for Neovim features
- Preserve teaching comments (this is an educational config)

## Key Dependencies

- **Plugin Manager:** lazy.nvim
- **LSP:** nvim-lspconfig, mason.nvim, mason-lspconfig.nvim
- **Completion:** blink.cmp, LuaSnip
- **Fuzzy Finder:** telescope.nvim
- **Treesitter:** nvim-treesitter
- **Formatting:** conform.nvim (with stylua for Lua)
- **Git:** gitsigns.nvim
- **Colorscheme:** tokyonight.nvim

## Common Patterns

### Adding a New Plugin

Add to the `require('lazy').setup({})` table in `init.lua`:
```lua
{
  'author/plugin-name',
  event = 'VimEnter',  -- lazy load on event
  opts = {},           -- passed to setup()
  config = function()
    -- custom setup
  end,
},
```

### Adding a Keymap

```lua
vim.keymap.set('n', '<leader>x', function()
  -- action
end, { desc = 'Description of action' })
```

### Configuring an LSP

Add to `servers` table in LSP configuration:
```lua
servers = {
  server_name = {
    settings = {
      -- server-specific settings
    },
  },
}
```

## Important Notes

1. **No breaking changes to init.lua:** This is a teaching configuration. Changes should maintain clarity and readability.

2. **Lazy loading:** Prefer event-based or key-based lazy loading to keep startup fast.

3. **Compatibility:** Support both stable and nightly Neovim (0.10+). Use version checks when needed:
   ```lua
   if vim.fn.has 'nvim-0.11' == 1 then
     -- nightly feature
   else
     -- stable fallback
   end
   ```

4. **Comments are features:** Don't remove teaching comments without good reason. They're intentional.

5. **Modularization:** Users can modularize, but the main config stays single-file by design.

## External Tools Required

- `git` - Version control
- `make` - Building native extensions
- `unzip` - Extracting archives
- `gcc` or compatible C compiler - Compiling treesitter parsers
- `ripgrep` (rg) - Fast text search for Telescope
- `fd-find` - Fast file finder (optional but recommended)
- `stylua` - Lua code formatter (installed via Mason)

## References

- Main documentation: `init.lua` comments
- Neovim help: `:help` or `:help lua-guide`
- Lazy.nvim docs: https://lazy.folke.io
- Kickstart README: `README.md`
