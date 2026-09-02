# Grok in Neovim with OpenCode and LazyVim

Two layers: **OpenCode uses Grok as the model**, then **LazyVim talks to OpenCode**. You are not running `grok agent stdio` for this path.

## 1. Point OpenCode at Grok

Install OpenCode, then connect xAI:

```bash
curl -fsSL https://opencode.ai/install | bash
opencode
```

In the TUI:

```
/connect
```

Pick **xAI**. SuperGrok OAuth is the subscription path; otherwise an `XAI_API_KEY` works.

Pin the model in `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "xai/grok-4"
}
```

Use `/models` in OpenCode if you want a different Grok id. Auth lives in `~/.local/share/opencode/auth.json`.

## 2. LazyVim: Sidekick (simplest)

LazyVim already treats **opencode** and **grok** as CLI tools in the **sidekick** extra.

1. `:LazyExtras` → enable `ai.sidekick`
2. Restart Neovim
3. `<leader>as` (select CLI) → **opencode**
4. `<leader>aa` toggles the CLI pane

From there OpenCode uses the Grok model you set above. `<leader>af` / `<leader>av` send the current file or visual selection.

That is the stock LazyVim path. No extra plugin required.

## 3. LazyVim: opencode.nvim (tighter editor context)

If you want `@this` / `@buffer` / diff review instead of a raw terminal, add `~/.config/nvim/lua/plugins/opencode.lua`:

```lua
return {
  {
    "nickjvandyke/opencode.nvim",
    version = "*",
    dependencies = {
      { "folke/snacks.nvim", opts = { input = {}, picker = {}, terminal = {} } },
    },
    config = function()
      vim.g.opencode_opts = {}
      vim.o.autoread = true

      vim.keymap.set({ "n", "x" }, "<C-a>", function()
        require("opencode").ask("@this: ")
      end, { desc = "Ask OpenCode" })
      vim.keymap.set({ "n", "x" }, "<C-x>", function()
        require("opencode").select()
      end, { desc = "Select OpenCode action" })
    end,
  },
}
```

The plugin starts `opencode --port` if none is running (snacks terminal is a good fit). `:checkhealth opencode` after install.

Typical keys:

| Action | Default in the snippet |
| --- | --- |
| Ask with cursor/selection | `<C-a>` |
| Prompt picker | `<C-x>` |

Placeholders: `@this`, `@buffer`, `@diagnostics`, `@visible`.

## 4. Do not mix with `grok agent stdio`

| Goal | Command / tool |
| --- | --- |
| Grok **inside OpenCode** (this setup) | `opencode` + `/connect` xAI |
| Grok **Build TUI** in Neovim | Sidekick → **grok**, or `compuficial/grok.nvim` |
| ACP client talking to Grok directly | `grok agent stdio` (Avante / CodeCompanion, not OpenCode) |

OpenCode’s ACP command is `opencode acp`. That is OpenCode as the agent, not Grok Build.

## Practical recipe

1. Connect xAI in OpenCode.
2. Enable LazyVim `ai.sidekick`.
3. Pick **opencode**.
4. Add `opencode.nvim` only if you want Neovim-native prompts and diffs.
