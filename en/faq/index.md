---
title: "FAQ"
---

Here are some frequently-asked questions.

#### Can I get smart autocompletion (intellisense/LSP)?
Check out the [LSP plugin](https://github.com/jgmdev/lite-xl-lsp).

#### Where is the integrated terminal?
Lite XL does not come with a terminal due to it's complexity.
For now, we have the [console plugin](https://github.com/franko/console) to run a command and dump its output. (not interactive)

#### Tabs and indent size?
In your user config (the cog icon in the file tree):
```lua
config.tab_type = "soft" -- soft for spaces, hard for real tabs (\t)
config.indent_size = 4   -- 4 spaces
```

#### How to bind commands to keys?
```lua
local keymap = require "core.keymap"
keymap.add { ["ctrl+escape"] = "core:quit" }
```

#### How to unbind commands for certain keys?
```lua
-- the second parameter lets you override commands for certain keys
-- in this case it maps it to nothing
keymap.add({ ["ctrl+escape"] = {} }, true)
```

#### How to get commands for those keybinds?
You can search for commands in the command palette.

For each command, replace the spaces in the right side with dashes.

For example: `Core: Find Command` → `core:find-command`

#### Vim mode?
[you need to vibe.](https://github.com/eugenpt/lite-xl-vibe)

#### Plugin recommendations
Just in case you don't want to comb through our [plugin repository](https://github.com/lite-xl/lite-plugins),
these are a list of plugins that just makes Lite XL a lot more pleasant.

Use case | Plugin
---------|-------
Automatically insert closing brackets and quotes | [autoinsert](https://github.com/lite-xl/lite-plugins/blob/master/plugins/autoinsert.lua?raw=1)
Highlight matching brackets | [bracketmatch](https://github.com/lite-xl/lite-plugins/blob/master/plugins/bracketmatch.lua?raw=1)
Ephemeral tabs (previewing files without creating multiple tabs) | [ephemeraldocviews](https://github.com/lite-xl/lite-plugins/blob/master/plugins/ephemeraldocviews.lua?raw=1)
Git diff gutter | [gitdiff_highlight](https://github.com/vincens2005/lite-xl-gitdiff-highlight)
Copy/Paste lines when nothing is selected | [linecopypaste](https://github.com/lite-xl/lite-plugins/blob/master/plugins/linecopypaste.lua?raw=1)
Linter support | [lint+](https://github.com/liquid600pgm/lintplus)
Minimap | [minimap](https://github.com/lite-xl/lite-plugins/blob/master/plugins/minimap.lua?raw=1)
Highlight code that matches the selection | [selectionhighlight](https://github.com/lite-xl/lite-plugins/blob/master/plugins/selectionhighlight.lua?raw=1)
Discord rich presence | [lite-xl-discord](https://github.com/vincens2005/lite-xl-discord)


#### Where's feature X? How about Y?
You can get more info in the [Features page]({% link en/features/index.md %})
