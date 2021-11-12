---
title: "Syntax Highlighting"
---

### How to create syntax highlighting plugins for Lite XL

Syntax highlighting plugins for Lite XL are lua files, that define some regular expressions that
match different parts of a given language, assigning different token types to each match.
These different token types are then given different colors by your chosen color scheme.

Like other plugins, syntax definitions are sourced from the following folders, in order:

- /usr/share/lite-xl/plugins/
- $HOME/.config/lite-xl/plugins/

NOTE: The exact location of these folders will depend on your OS and installation method. For example, on Windows, the variable `$USERPROFILE` will be used instead of `$HOME`.

The user module folder for Lite XL can generally be found in these places on different OSes:

- Windows: C:\Users\(username)\.config\lite-xl\
- MacOS: /Users/(usernmame)/.config/lite-xl/
- Linux: /home/(username)/.config/lite-xl/

So, to create a new syntax definition on Linux, you can just create a `.lua` file in your `$HOME/.config/lite-xl/plugins/` folder.

#### What syntax token types are there?

These are defined by your color scheme. The ones defined by `lite-xl/core/style.lua` are:

```lua
style.syntax["normal"] = { common.color "#e1e1e6" }
style.syntax["symbol"] = { common.color "#e1e1e6" }
style.syntax["comment"] = { common.color "#676b6f" }
style.syntax["keyword"] = { common.color "#E58AC9" }
style.syntax["keyword2"] = { common.color "#F77483" }
style.syntax["number"] = { common.color "#FFA94D" }
style.syntax["literal"] = { common.color "#FFA94D" }
style.syntax["string"] = { common.color "#f7c95c" }
style.syntax["operator"] = { common.color "#93DDFA" }
style.syntax["function"] = { common.color "#93DDFA" }
```

This is, honestly, not a lot of token types.

You can define _any token type you like_ in your syntax definition - but to have any effect, the token
type will need to be supported by the color scheme. If the color scheme _doesn't_ assign a color to a token type,
then it won't be highlighted.

Let's walk through an example syntax definition and see how it works.

### Example syntax: ssh config files

This is a small, simple example of a syntax definition. It's intended to highlight SSH Config files and looks like this:

```lua
-- mod-version:2 -- lite-xl 2.0
local syntax = require "core.syntax"

syntax.add {
  files = { "sshd?/?_?config$" },
  comment = '#',
  patterns = {
    { pattern = "#.*\n",        type = "comment" },
    { pattern = "%d+",          type = "number"  },
    { pattern = "[%a_][%w_]*",  type = "symbol"  },
    { pattern = "@",            type = "literal" },
  },
  symbols = {
    -- ssh config
    ["Host"]                         = "function",
    ["ProxyCommand"]                 = "function",

    ["HostName"]                     = "keyword",
    ["IdentityFile"]                 = "keyword",
    ...

    -- sshd config
    ["Subsystem"]                    = "keyword2",

    -- Literals
    ["yes"]      = "literal",
    ["no"]       = "literal",
    ["any"]      = "literal",
    ["ask"]      = "literal",
  },
}
```

Let's take each section in turn and see how it works.

#### Header

The first line is a Lua comment & tells Lite XL which version this plugin requires. The second imports the `core.syntax` module
for us to use:

```lua
-- mod-version:2 -- lite-xl 2.0
local syntax = require "core.syntax"
```

We then add a syntax definition to lite, using `syntax.add {...}`. The contents of this definition are covered next.

#### Files

The `files` property tells Lite XL which files this syntax should be used for. This is a Lua pattern that matches against the full path of the file being opened. For example, to match against Markdown files - with either a `.md` or a `.markdown` extension,
you could do this:

```lua
files = { "%.md$", "%.markdown$" },
```

In our original example, we match against the end of the path rather than the extension, because SSH config files don't have extensions - and we don't want to match all `config` files. We expect the path for SSH config files to look something like one of these:

- `~/.ssh/config`
- `/etc/ssh/ssh_config`
- `/etc/ssh/sshd_config`

This pattern matches paths that look like that:

```lua
files = { "sshd?/?_?config$" },
```

#### Comment

The comment property _doesn't_ define which parts of the syntax are comments - see Patterns for that, below. This property tells Lite XL which character to insert at the start of selected lines when you press `ctrl+/`.

#### Patterns

Each matching pattern captures the text that it matches - so a given run of text can only match one pattern.
Patterns are tested in the order that they are written in the syntax definition, so the first match will win.

Each pattern takes one of the following forms:

##### Simple Pattern

```lua
{ pattern = "#.*\n",        type = "comment" },
```

This form matches the line against the pattern and if it matches, assigns the matching text to the given token `type` - `comment`, in this case.

##### Start & End Pattern

```lua
{ pattern = { "%[", "%]" }, type = "keyword" },
```

This form has two patterns - one that matches against the start of the range and one that matches against the end. Everything between the start and the end will be assigned the given token `type`.

##### Start & End Pattern, with Escape

```lua
{ pattern = { '"', '"', '\\' }, type = "string" },
```

This is the same as the previous form, but with an extra, third parameter.
The 3rd part, the `'\\'` part in this example, specifies the character that allows escaping the closing match.

For more on Lua Patterns, see: [Lua Pattern Reference](https://www.lua.org/manual/5.3/manual.html#6.4.1)

If you need to use PCRE Regular Expressions, instead of Lua Patterns, you can use the `regex` keyword here, instead of `pattern`.

#### Symbols

The symbols section allows you to assign token types to particular keywords or strings - usually reserved words in the language you are highlighting.

For example this highlights `Host` using the `function` token type, `HostName` as a `keyword` and `yes`, `no`, `any` & `ask` as a `literal`.

```lua
["Host"]                         = "function",
["HostName"]                     = "keyword",

["yes"]      = "literal",
["no"]       = "literal",
["any"]      = "literal",
["ask"]      = "literal",
```
