<!-- Delete later: continue at line 161 -->

# Syntaxhervorhebung

## So erstellt man Syntaxhervorhebung für Lite XL
Syntaxhervorhebung Plugins für Lite XL sind Lua Dateien. Diese Dateien definieren Muster oder Regex
verschiedene teile einer gegebenen Sprache, man ordnet Token-Typen zu Übereinstimmung zu.
Diese verschiedenen Token-Typen werden dann verschiedene farben von deinem ausgesuchten Color Scheme gegeben.

Wie andere Plugins, Syntax Definitionen werden von den folgenden Ordnern empfangen, in der folgenden Reihenfolge:

- `/usr/share/lite-xl/plugins/`
- `$HOME/.config/lite-xl/plugins/`

BEMERKE: Der genaue Ort von diesen Ordnern wird von dein Betriebssystem und Installationsmethode abhängen. Zum Beispiel, unter Windows wird das Variable `$USERPROFILE` benutzt werden anstatt `$HOME`.

Der Benutzer Modul Ordner für Lite Xl kann in diesen Orten auf different Betriebssystemen gefunden werden:

- Windows: `C:\Users\(nutzername)\.config\lite-xl`
- MacOS: `/Users/(nutzername)/.config/lite-xl`
- Linux: `/home/(nutzername)/.config/lite-xl`

Also, um eine neue Syntax Definition auf Linux zu erstellen, musst du eine `.lua` Datei in dein `$HOME/.config/lite-xl/plugins/` Ordner machen.

## Welche Syntax-Token arten sind unterstützt?

Die unterstützten Syntax_Token art, definiert von `lite-xl/core/style.lua`, sind:

- normal
- symbol
- comment
- keyword
- keyword2
- number
- literal
- string
- operator
- function

In dein Syntaxhervorhebung Plugin, schreibst du Muster um Teile der Sprachen-Syntax zu entsprechen, und um Token-Typen zu übereinstimmen. Du musst nicht alle benutzen - benutze so viele die du brauchst für deine Sprache.

Let's walk through an example syntax definition and see how this works.

Gehen wir mal durch eine Beispiel Syntax Definition und wir werden sehen wie es funktioniert.

## Beispiel Syntax: ssh config Dateien

Das ist ein kleines, simples Beispiel von einer Syntax Definition, Es soll eine SSH config Datei hervorheben und es sieht so aus:

```lua
-- mod-version:2 -- lite-xl 2.0
local syntax = require "core.syntax"

syntax.add {
  files = { "sshd?/?_?config$" },
  comment = '#',
  patterns = {
    { pattern = "#.*\n",        type = "comment"  },
    { pattern = "%d+",          type = "number"   },
    { pattern = "[%a_][%w_]*",  type = "symbol"   },
    { pattern = "@",            type = "operator" },
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

Schauen wir uns mal jeden Teil an und schauen wie es funktioniert.

### Header

Die erste Zeile ist ein Lua kommentar und sagt Lite XL welche version dieses Plugin braucht. Die zweite Zeile importiert das `core.syntax` Modul
dass wir nutzen können:

```lua
-- mod-version:2 -- lite-xl 2.0
local syntax = require "core.syntax"
```

Dann fügen wir eine Syntax Definition mit `syntax.add {...}` zu lite ein.

#### Files

Die `files` Eigenschaft sagt Lite XL welche Dateien these Syntax benutzt werden soll. Das ist ein Lua Muster, das mit dem vollständigen Pfad der geöffneten Datei übereinstimmt. Zum Beispiel, um gegen Markdown Dateien zu übereinstimmen - mit entweder eine `.md` oder eine `.markdown` Erweiterung,
du könntest das machen:

```lua
files = { "%.md$", "%.markdown$" },
```

In unseren original Beispiel, gleichen wir mit dem Ende des Pfads und nicht mit der Erweiterung ab, weil SSH config Dateien keine Erweiterung hat - und wir nicht alle `config` Dateien abgleichen. Wir erwarten den Pfad für SSH config Dateien so auszusehen:

- `~/.ssh/config`
- `/etc/ssh/ssh_config`
- `/etc/ssh/sshd_config`

Dieses Muster gleicht Pfade ab die so aussehen:

```lua
files = { "sshd?/?_?config$" },
```

### Kommentare

Die Kommentar Eigenschaft definiert _nicht_ welche Teile der Syntax Kommentare sind - Schaue auf Muster für das unten. Diese Eigenschaft sagt Lite XL welche Charaktere beim Anfang der ausgewählten Zeilen hinzufügt werden sollen, wenn du `ctrl+/` drückst.
Du kannst auch `block_comment` benutzen um Lite XL zu sagen, wie es multiline oder Block Kommentare machen soll.

### Muster

Ein gegebener Textabschnitt kann nur ein Muster abgleicht werden. Wenn Lite XL einmal einschieden hat, dass es mit einem Muster übereinstimmt, dann wird es den Token-Typen zuweisen und es wird weitergehen.
Muster werden getestet in der Reihenfolge wie es in der Syntax Definition geschrieben wurde, also wird das erste Übereinstimmen gewinnen.

Jedes Muster nimmt einer dieser Formen an:

#### Einfaches Muster

```lua
{ pattern = "#.*\n",        type = "comment" },
```

Diese Form gleicht die Zeile mit dem Muster ab und wenn es abstimmt, weist es in diesem Fall den passenden Text den gebenen Token `type` - `comment` zu.

#### Start- & Endmuster

```lua
{ pattern = { "%[", "%]" }, type = "keyword" },
```

Diese Form hat zwei Muster - eines das mit dem Anfang des Bereichs übereinstimmt und eines dass mit dem Ende übereinstimmt. Alles zwischen den Anfang und den Ende wird den Token `type` zugewiesen.

#### Start- & Endmuster, mit Ausgang

```lua
{ pattern = { '"', '"', '\\' }, type = "string" },
```

Dieses ist das Gleiche wie die letzte Form, aber mit einem dritten Parameter.
Der dritte Teil, der `'\\'` Teil in diesem Beispiel, spezifiziert den Charakter dass entkommen vom Schlussübereinstimmung ermöglicht.

Für mehr Information von Lua Muster, sehe [Lua Muster Referenz (English)](https://www.lua.org/manual/5.3/manual.html#6.4.1)

Wenn du PCRE Regular Expressions anstatt Lua Muster benutzen musst, kannst du das Stichwort `regex` anstelle von `pattern` benutzen.

### Symbole

> Dieser Teil ist **nicht mit dem `symbol` Token-Typ verwandt**.

The symbols section allows you to assign token types to particular keywords or strings - usually reserved words in the language you are highlighting.
The token type in this section **always take precedence** over token types declared in patterns.

For example this highlights `Host` using the `function` token type, `HostName` as a `keyword` and `yes`, `no`, `any` & `ask` as a `literal`:

```lua
["Host"]                         = "function",
["HostName"]                     = "keyword",

["yes"]      = "literal",
["no"]       = "literal",
["any"]      = "literal",
["ask"]      = "literal",
```

#### Tips: double check your patterns!

There are a few common mistakes that can be made when using the `symbols` table in conjunction with patterns.

##### Case 1: Spaces between two `symbols` tokens

Let's have an example:

```lua
{ pattern = "[%a_][%w_]+%s+()[%a_][%w_]+", type = { "keyword2", "symbol" } }
```

Let's explain the pattern a bit (omitting the empty parentheses):

```
[%a_] = any alphabet and underscore
[%w_] = any alphabet, numbers and underscore
%s = any whitespace character

WORD =
  [%a_] followed by (1 or more [%w_])

pattern =
  WORD followed by (one or more %s) followed by WORD
```

Afterwards, you add an entry `["my"] = "literal"` in the `symbols` table.
You test the syntax with `my function` found that `"my"` isn't highlighted as `literal`. Why did that happen?

**`symbols` table requires an exact match**.
If you look carefully, the empty parentheses (`()`) is placed **after the space**!
This tells Lite XL that `WORD followed by (one or more %s)` is a token, which will match `my ` (note the space in the match).

The fix is to add a `normal` token for the whitespace between the two tokens:

```lua
{ pattern = "[%a_][%w_]+()%s+()[%a_][%w_]+", type = { "keyword2", "normal", "symbol" } }
```

##### Case 2: Patterns & `symbols` tokens

One might assume that Lite XL magically matches text against the `symbols` table. This is not the case.

In some languages, people may add a generic pattern to delegate the matching to the `symbols` table.

```lua
{ pattern = "[%a_][%w_]*", "symbol" }
```

However, the `symbols` table may look like this:

```lua
symbols = {
  ["my-symbol"] = "function",
  ["..something_else"] = "literal"
}
```

`"my-symbol` contains a dash (`-`) and `"..something_else"` contains 2 dots (`.`).
None of the characters are matched by `[%a_][%w_]*`!

**Beware of the text you intend to match in the `symbols` table.**
**If you want to use it, you need to ensure that it can matched by one of the patterns.**

The correct patterns are:

```lua
{ pattern = "[%a_][%w%-_]*", "symbol" },
{ pattern = "%.%.[%a_][%w_]*", "symbol" },
```

## Testing Your New Syntax

To test your new syntax highlighting you need to do two things:

- Reload the Lite XL core
- Load a file in your chosen language and see how it looks

To reload the core, you can either restart Lite XL, or reload the core from the command palette, without needing to restart.
To do this, type `ctrl+shit+p` to show the command palette, then select `Core: Restart` (or type `crr` or something similar to match it), then press Enter. You will need to restart the core after any changes you make to the syntax highlighting definition.


## Example advanced syntax: Markdown

> **Note: This example has features from 2.1. It is not compatible with older versions of lite-xl.**

Not all languages are as simple as SSH config files. Markup languages like HTML and Markdown are especially hard to parse correctly. Here's the markdown syntax file in its full glory:

```lua
-- mod-version:3
local syntax = require "core.syntax"
local style = require "core.style"
local core = require "core"

local initial_color = style.syntax["keyword2"]

-- Add 3 type of font styles for use on markdown files
for _, attr in pairs({"bold", "italic", "bold_italic"}) do
  local attributes = {}
  if attr ~= "bold_italic" then
    attributes[attr] = true
  else
    attributes["bold"] = true
    attributes["italic"] = true
  end
  -- no way to copy user custom font with additional attributes :(
  style.syntax_fonts["markdown_"..attr] = renderer.font.load(
    DATADIR .. "/fonts/JetBrainsMono-Regular.ttf",
    style.code_font:get_size(),
    attributes
  )
  -- also add a color for it
  style.syntax["markdown_"..attr] = style.syntax["keyword2"]
end

local in_squares_match = "^%[%]"
local in_parenthesis_match = "^%(%)"

syntax.add {
  name = "Markdown",
  files = { "%.md$", "%.markdown$" },
  block_comment = { "<!--", "-->" },
  space_handling = false, -- turn off this feature to handle it our selfs
  patterns = {
  ---- Place patterns that require spaces at start to optimize matching speed
  ---- and apply the %s+ optimization immediately afterwards
    -- bullets
    { pattern = "^%s*%*%s",                 type = "number" },
    { pattern = "^%s*%-%s",                 type = "number" },
    { pattern = "^%s*%+%s",                 type = "number" },
    -- numbered bullet
    { pattern = "^%s*[0-9]+[%.%)]%s",       type = "number" },
    -- blockquote
    { pattern = "^%s*>+%s",                 type = "string" },
    -- alternative bold italic formats
    { pattern = { "%s___", "___%f[%s]" },   type = "markdown_bold_italic" },
    { pattern = { "%s__", "__%f[%s]" },     type = "markdown_bold" },
    { pattern = { "%s_[%S]", "_%f[%s]" },   type = "markdown_italic" },
    -- reference links
    {
      pattern = "^%s*%[%^()["..in_squares_match.."]+()%]: ",
      type = { "function", "number", "function" }
    },
    {
      pattern = "^%s*%[%^?()["..in_squares_match.."]+()%]:%s+.+\n",
      type = { "function", "number", "function" }
    },
    -- optimization
    { pattern = "%s+",                      type = "normal" },

  ---- HTML rules imported and adapted from language_html
  ---- to not conflict with markdown rules
    -- Inline JS and CSS
    {
      pattern = {
      "<%s*[sS][cC][rR][iI][pP][tT]%s+[tT][yY][pP][eE]%s*=%s*" ..
        "['\"]%a+/[jJ][aA][vV][aA][sS][cC][rR][iI][pP][tT]['\"]%s*>",
      "<%s*/[sS][cC][rR][iI][pP][tT]>"
      },
      syntax = ".js",
      type = "function"
    },
    {
      pattern = {
      "<%s*[sS][cC][rR][iI][pP][tT]%s*>",
      "<%s*/%s*[sS][cC][rR][iI][pP][tT]>"
      },
      syntax = ".js",
      type = "function"
    },
    {
      pattern = {
      "<%s*[sS][tT][yY][lL][eE][^>]*>",
      "<%s*/%s*[sS][tT][yY][lL][eE]%s*>"
      },
      syntax = ".css",
      type = "function"
    },
    -- Comments
    { pattern = { "<!%-%-", "%-%->" },   type = "comment" },
    -- Tags
    { pattern = "%f[^<]![%a_][%w_]*",    type = "keyword2" },
    { pattern = "%f[^<][%a_][%w_]*",     type = "function" },
    { pattern = "%f[^<]/[%a_][%w_]*",    type = "function" },
    -- Attributes
    {
      pattern = "[a-z%-]+%s*()=%s*()\".-\"",
      type = { "keyword", "operator", "string" }
    },
    {
      pattern = "[a-z%-]+%s*()=%s*()'.-'",
      type = { "keyword", "operator", "string" }
    },
    {
      pattern = "[a-z%-]+%s*()=%s*()%-?%d[%d%.]*",
      type = { "keyword", "operator", "number" }
    },
    -- Entities
    { pattern = "&#?[a-zA-Z0-9]+;",         type = "keyword2" },

  ---- Markdown rules
    -- math
    { pattern = { "%$%$", "%$%$", "\\"  },  type = "string", syntax = ".tex"},
    { pattern = { "%$", "%$", "\\" },       type = "string", syntax = ".tex"},
    -- code blocks
    { pattern = { "```c++", "```" },        type = "string", syntax = ".cpp" },
    -- ... there's some other patterns here, but I removed them for brevity
    { pattern = { "```lobster", "```" },    type = "string", syntax = ".lobster" },
    { pattern = { "```", "```" },           type = "string" },
    { pattern = { "``", "``" },             type = "string" },
    { pattern = { "%f[\\`]%`[%S]", "`" },   type = "string" },
    -- strike
    { pattern = { "~~", "~~" },             type = "keyword2" },
    -- highlight
    { pattern = { "==", "==" },             type = "literal" },
    -- lines
    { pattern = "^%-%-%-+\n",               type = "comment" },
    { pattern = "^%*%*%*+\n",               type = "comment" },
    { pattern = "^___+\n",                  type = "comment" },
    -- bold and italic
    { pattern = { "%*%*%*%S", "%*%*%*" },   type = "markdown_bold_italic" },
    { pattern = { "%*%*%S", "%*%*" },       type = "markdown_bold" },
    -- handle edge case where asterisk can be at end of line and not close
    {
      pattern = { "%f[\\%*]%*[%S]", "%*%f[^%*]" },
      type = "markdown_italic"
    },
    -- alternative bold italic formats
    { pattern = "^___[%s%p%w]+___%s" ,      type = "markdown_bold_italic" },
    { pattern = "^__[%s%p%w]+__%s" ,        type = "markdown_bold" },
    { pattern = "^_[%s%p%w]+_%s" ,          type = "markdown_italic" },
    -- heading with custom id
    {
      pattern = "^#+%s[%w%s%p]+(){()#[%w%-]+()}",
      type = { "keyword", "function", "string", "function" }
    },
    -- headings
    { pattern = "^#+%s.+\n",                type = "keyword" },
    -- superscript and subscript
    {
      pattern = "%^()%d+()%^",
      type = { "function", "number", "function" }
    },
    {
      pattern = "%~()%d+()%~",
      type = { "function", "number", "function" }
    },
    -- definitions
    { pattern = "^:%s.+",                   type = "function" },
    -- emoji
    { pattern = ":[a-zA-Z0-9_%-]+:",        type = "literal" },
    -- images and link
    {
      pattern = "!?%[!?%[()["..in_squares_match.."]+()%]%(()["..in_parenthesis_match.."]+()%)%]%(()["..in_parenthesis_match.."]+()%)",
      type = { "function", "string", "function", "number", "function", "number", "function" }
    },
    {
      pattern = "!?%[!?%[?()["..in_squares_match.."]+()%]?%]%(()["..in_parenthesis_match.."]+()%)",
      type = { "function", "string", "function", "number", "function" }
    },
    -- reference links
    {
      pattern = "%[()["..in_squares_match.."]+()%] *()%[()["..in_squares_match.."]+()%]",
      type = { "function", "string", "function", "function", "number", "function" }
    },
    {
      pattern = "!?%[%^?()["..in_squares_match.."]+()%]",
      type = { "function", "number", "function" }
    },
    -- url's and email
    {
      pattern = "<[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+%.[a-zA-Z0-9-.]+>",
      type = "function"
    },
    { pattern = "<https?://%S+>",           type = "function" },
    { pattern = "https?://%S+",             type = "function" },
    -- optimize consecutive dashes used in tables
    { pattern = "%-+",                      type = "normal" },
  },
  symbols = { },
}

-- Adjust the color on theme changes
core.add_thread(function()
  while true do
    if initial_color ~= style.syntax["keyword2"] then
      for _, attr in pairs({"bold", "italic", "bold_italic"}) do
        style.syntax["markdown_"..attr] = style.syntax["keyword2"]
      end
      initial_color = style.syntax["keyword2"]
    end
    coroutine.yield(1)
  end
end)
```

### Syntax fonts (Since 1.16.10)

The syntax allows users to set different font styles (bold, italic, etc.) for different patterns.
To change the font style of a token, add a Font to `style.syntax_fonts[token_type]`.
For example:
```
-- will ensure every "fancysyntax_fancy_token" is italic
style.syntax_fonts["fancysyntax_fancy_token"] = renderer.font.load("myfont.ttf", 14 * SCALE, { italic = true })
```

The markdown example automates this with a for loop.

The limitations here are that fonts cannot be copied with different attributes, thus the font path has to be hardcoded.
Other than that, abusing `style.syntax_fonts` may lead to **slow performance** and **high memory consumption**.
This is very obvious when the user tries to resize the editor with `ctrl-scroll` or `ctrl+` and `ctrl-`.
Please use it in moderation.

### Space handling (v2.1 (upcoming) / `master`)

By default, Lite XL prepends a pattern `{ pattern = "%s+", type = "normal" }` to the syntax.
This improves the performance drastically on lines that starts with whitespaces (eg. heavily indented lines)
by matching the whitespace before other patterns in order to prevent Lite XL from iterating the entire syntax.
However, there may be syntaxes that require matching spaces (eg. Markdown with indented blocks)
so this can be disabled by setting `space_handling` to `false.`

> To keep the space handling optimization or to support older versions of Lite XL,
> `{ pattern = "%s+", type = "normal" }` can be added after patterns that require space.

### Simple patterns with multiple tokens (v1.16.10)

This is an excerpt taken from the markdown plugin:

```lua
local in_squares_match = "^%[%]"
-- reference links
{
  pattern = "^%s*%[%^()["..in_squares_match.."]+()%]: ",
  type = { "function", "number", "function" }
},
```

Sometimes it makes sense to highlight different parts of a pattern differently.
An empty parentheses (`()`) in Lua patterns will return the position of the text in the parentheses.
This will tell Lite XL when to change the type of token.
For instance, `^%s*%[%^` is `"function"`, `["..in_squares_match.."]+` is `"number"` and `%]: ` is `"function"`.

### Subsyntaxes (Since v1.16.10)

Lite XL supports embedding another syntax into the existing syntax.
This is used to support code blocks inside the markdown syntax.

For example:
```lua
{ pattern = { "```cpp", "```" },        type = "string", syntax = ".cpp" },
```

This would highlight `` ```cpp `` and `` ``` `` with `"string"` while everything inside them will be highlighted with a syntax that matches `".cpp"`.
