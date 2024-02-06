# Usage

Lite XL is a lightweight text editor written mostly in Lua — it aims to provide
something practical, pretty, *small* and fast, implemented as simply as
possible; easy to modify and extend, or to use without doing either.

Lite XL is based on the Lite editor and provide some enhancements
while remaining generally compatible with it.

## Getting Started

Lite XL works using a *project directory* — this is the directory where your
project's code and other data resides.

To open a specific project directory the directory name can be passed
as a command-line argument *(`.` can be passed to use the current directory)*
or the directory can be dragged onto either the executable or a running instance.

Once started the project directory can be changed using the command
`core:change-project-folder`. The command will close all the documents
currently opened and switch to the new project directory.

If you want to open a project directory in a new window the command
`core:open-project-folder` will open a new editor window with the selected
project directory.

The main way of opening files in Lite XL is through the `core:find-file` command
— this provides a fuzzy finder over all of the project's files and can be
opened using the `ctrl`+`p` shortcut by default.

Commands can be run using keyboard shortcuts, or by using the `core:find-command`
command bound to `ctrl`+`shift`+`p` by default. For example, pressing
the above combination and typing `newdoc` then pressing `return`
would open a new document. The current keyboard shortcut for a command
can be seen to the right of the command name on the command finder, thus to find
the shortcut for a command `ctrl`+`shift`+`p` can be pressed
and the command name typed.

## User Data Directories

Lite XL uses standard systems user directories; the user data can be found in
`$HOME/.config/lite-xl` on Linux and macOS.
On Windows, the variable `$USERPROFILE` will be used instead of
`$HOME`.

## User Module

Lite XL can be configured through use of the user module. The user module can be
used for changing options in the config module, adding additional key bindings,
loading custom color themes, modifying the style or changing any other part of
the editor to your personal preference.

The user module is loaded when the application starts,
after the plugins have been loaded.

The user module can be modified by running the `core:open-user-module` command
or otherwise directly opening the `$HOME/.config/lite-xl/init.lua` file.

On Windows, the variable `$USERPROFILE` will be used instead of
`$HOME`.

**tl;dr:**

- Windows: `C:\Users\(username)\.config\lite-xl\init.lua`
- MacOS: `/Users/(usernmame)/.config/lite-xl/init.lua`
- Linux: `/home/(username)/.config/lite-xl/init.lua`

These aren't the exact location, but it gives you an idea where to find.

Please note that Lite XL differs from the standard Lite editor for the location
of the user's module.

## Project Module

The project module is an optional module which is loaded from the current
project's directory when Lite XL is started. Project modules can be useful for
things like adding custom commands for project-specific build systems, or
loading project-specific plugins.

The project module is loaded when the application starts,
after both the plugins and user module have been loaded.

The project module can be edited by running the `core:open-project-module`
command — if the module does not exist for the current project when the
command is run it will be created.

## Add directories to a project

In addition to the project directories it is possible to add other directories
using the command `core:add-directory`.
Once added a directory it will be shown in the tree-view on the left side and
the additional files will be reachable using the `ctrl`+`p` command (find file).
The additonal files will be also visible when searching across the project.

The additional directories can be removed using the command `core:remove-directory`.

When you will open again Lite XL on the same project folder the application will
remember your workspace including the additonal project directories.

Since version 1.15 Lite XL does not need a workspace plugin as it is now
bundled with the editor.

## Create new empty directory

Using the command `files:create-directory` or control-click in a directory in the
tree-view to create a new empty subdirectory.

## Commands

Commands are used both through the command finder (`ctrl`+`shift`+`p`) and
by Lite XL's keyboard shortcut system. Commands consist of 3 components:

* **Name** — The command name in the form of `namespace:action-name`, for
  example: `doc:select-all`
* **Predicate** — A function that returns true if the command can be ran, for
  example, for any document commands the predicate checks whether the active
  view is a document
* **Function** — The function which performs the command itself

Commands can be added using the `command.add` function provided by the
`core.command` module:

```lua
local core = require "core"
local command = require "core.command"

command.add("core.docview", {
  ["doc:save"] = function()
    core.active_view.doc:save()
    core.log("Saved '%s'", core.active_view.doc.filename)
  end
})
```

Commands can be performed programatically (eg. from another command or by your
user module) by calling the `command.perform` function after requiring the
`command` module:

```lua
local command = require "core.command"
command.perform "core:quit"
```

### Keymap

All keyboard shortcuts are handled by the `core.keymap` module.
A key binding maps a "stroke" (eg. `ctrl`+`q`) to one or more commands
(eg. `core:quit`). When the shortcut is pressed Lite XL will iterate each command
assigned to that key and run the *predicate function* for that command — if the
predicate passes it stops iterating and runs the command.

An example of where this used is the default binding of the `tab` key:

``` lua
  ["tab"] = { "command:complete", "doc:indent" },
```

When tab is pressed the `command:complete` command is attempted which will only
succeed if the command-input at the bottom of the window is active. Otherwise
the `doc:indent` command is attempted which will only succeed if we have a
document as our active view.

A new mapping can be added by your user module as follows:

```lua
local keymap = require "core.keymap"
keymap.add { ["ctrl+q"] = "core:quit" }
```

A list of default mappings can be viewed [here][1].

### Global variables

There are a few global variables set by the editor.
These variables are available everywhere and shouldn't be overwritten.

- `ARGS`: command-line arguments. `argv[1]` is the program name, `argv[2]` is the 1st parameter, ...
- `PLATFORM`: Output from `SDL_GetPlatform()`. Can be `Windows`, `Mac OS X`, `Linux`, `iOS` and `Android`.
- `SCALE`: Font scale. Usually 1, but can be higher on HiDPI systems.
- `EXEFILE`: An absolute path to the executable.
- `EXEDIR`: The executable directory. **DO NOT WRITE TO THIS DIRECTORY.**
- `VERSION`: lite-xl version.
- `MOD_VERSION`: mod-version used in plugins. This is usually incremented when there are API changes.
- `PATHSEP`: Path seperator. `\` (Windows) or `/` (Other OSes)
- `DATADIR`: The data directory, where the Lua part of lite-xl resides. **DO NOT WRITE TO THIS DIRECTORY.**
- `USERDIR`: User configuration directory.

> `USERDIR` should be used instead of `DATADIR` when configuring the editor
> because `DATADIR` might not be writable.
> (for example, if the editor is installed in `/usr`, `DATADIR` will be `/usr/share/lite-xl`!)
> `USERDIR` on the other hand should always be writable for the user, and allows multiple users to customize
> their own editor.

## Plugins

Plugins in Lite XL are normal lua modules and are treated as such — no
complicated plugin manager is provided, and, once a plugin is loaded, it is never
expected be to have to unload itself.

To install a plugin simply drop it in the `plugins` directory in the user
module directory.
When Lite XL starts it will first load the plugins included in the data directory
and will then loads the plugins located in the user module directory.

To uninstall a plugin the plugin file can be deleted — any plugin
(including those included with the default installation)
can be deleted to remove its functionality.

If you want to load a plugin only under a certain circumstance (for example,
only on a given project) the plugin can be placed somewhere other than the
`plugins` directory so that it is not automatically loaded. The plugin can
then be loaded manually as needed by using the `require` function.

Plugins can be downloaded from the [plugins repository][2].

## Restarting the editor

If you modify the user configuration file or some of the Lua implementation files
you may restart the editor using the command `core:restart`.
The entire application will be restarting by keeping the window that is already in use.

## Color Themes

Colors themes in Lite XL are lua modules which overwrite the color fields of
Lite XL's `core.style` module.
Pre-defined color methods are located in the `colors` folder in the data directory.
Additional color themes can be installed in the user's directory in a folder named
`colors`.

A color theme can be set by requiring it in your user module:

```lua
core.reload_module "colors.winter"
```

In the Lite editor the function `require` is used instead of `core.reload_module`.
In Lite XL `core.reload_module` should be used to ensure that the color module
is actually reloaded when saving the user's configuration file.

Color themes can be downloaded from the [color themes repository][3].
They are included with Lite XL release packages.


[1]: /en/documentation/keymap
[2]: https://github.com/lite-xl/lite-xl-plugins
[3]: https://github.com/lite-xl/lite-xl-colors
