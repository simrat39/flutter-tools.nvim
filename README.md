# flutter-tools.nvim

Build flutter and dart applications in neovim using the native lsp.

**Status: Alpha**

#### NOTE:

I plan to try and keep things _relatively_ stable but ultimately there might still need to be some
breaking changes while I flesh out some of the functionality.

# Inspiration

This plugin draws inspiration from [`emacs-lsp/lsp-dart`](https://github.com/emacs-lsp/lsp-dart), [`coc-flutter`](https://github.com/iamcco/coc-flutter) and [`nvim-metals`](https://github.com/scalameta/nvim-metals), the idea being
to allow users to easily develop flutter apps using neovim.

## Prerequisites

- `neovim 0.5+` (nightly)

## Installation

using `vim-plug`

```vim
Plug "akinsho/flutter-tools.nvim"
```

or using `packer.nvim`

```lua
use "akinsho/flutter-tools.nvim"
```

To set it up

```lua
require("flutter-tools").setup{} -- use defaults

-- alternatively you can override the default configs
require("flutter-tools").setup {
  experimental = { -- map of feature flags
    lsp_derive_paths = false, -- experimental: Attempt to find the user's flutter SDK
  },
  debugger = { -- experimental: integrate with nvim dap
    enabled = false,
  },
  flutter_path = "<full/path/if/needed>", -- <-- this takes priority over the lookup
  flutter_lookup_cmd = nil, -- example "dirname $(which flutter)" or "asdf where flutter"
  widget_guides = {
    enabled = false,
  },
  closing_tags = {
    highlight = "ErrorMsg",
    prefix = ">"
  },
  dev_log = {
    open_cmd = "tabedit",
  },
  outline = {
    open_cmd = "30vnew",
  },
  lsp = {
    on_attach = my_custom_on_attach,
    capabilities = my_custom_capabilities -- e.g. lsp_status capabilities
    settings = {
      showTodos = true,
      completeFunctionCalls = true -- NOTE: this is WIP and doesn't work currently
    }
  }
}
```

You can override any options available in the `lspconfig` setup, this call essentially wraps
it and adds some extra `flutter` specific handlers and utilisation options.

#### Flutter binary

In order to run flutter commands you _might_ need to pass either a _path_ or a _command_ to the plugin so it can find your
installation of flutter. Most people will not need this since it will find the executable path of `flutter` if it is in your `$PATH`.

If using something like `asdf` or some other version manager, or you installed flutter via `snap` or in some other custom way,
then you need to pass in a command by specifying `flutter_lookup_cmd = <my-command>`.
If you have a full path already you can pass it in using `flutter_path`.

If using a `snap` installation set your `flutter_lookup_cmd` to `"echo $HOME/snap/flutter/common/flutter/bin/flutter"`
which is where this is usually installed by `snap`. Alternatively you can use the `experimental.lsp_derive_paths` option
which should auto-magically find your flutter installation.

# Functionality

#### Run flutter app with hot reloading

![hot reload](./.github/hot_reload.gif)

#### Start emulators or connected devices

![flutter-devices](https://user-images.githubusercontent.com/22454918/112320203-b5f31a80-8ca6-11eb-90b8-9ac934a842da.png)

#### Visualise logs

![dev log](./.github/dev_log.png)

#### Widget guides (experimental, default: disabled)

![Widget guides](./.github/outline_guide.png)

NOTE: To configure the highlight colour you can override the `FlutterWidgetGuides` highlight group

#### Outline window

![Outline window](./.github/outline.gif)

#### Closing Tags

![closing tags](./.github/closing_tags.png)

# Usage

- `FlutterRun` - Run the current project. This needs to be run from within a flutter project.
- `FlutterDevices` - Brings up a list of connected devices to select from.
- `FlutterEmulators` - Similar to devices but shows a list of emulators to choose from.
- `FlutterReload` - Reload the running project
- `FlutterRestart` - Restart the current project
- `FlutterQuit` - Ends a running session
- `FlutterOutline` - Opens an outline window showing the widget tree for the given file

## Debugging (WIP)

_Requires nvim-dap_

```lua
-- with packer
use 'mfussenegger/nvim-dap'
```

This plugin integrates with [nvim-dap](https://github.com/mfussenegger/nvim-dap) to provide debug capabilities.
Currently if `debugger` is set to `true` in the user's config **it will expect `nvim-dap` to be installed**.
If `dap` is installed the plugin will attempt to install the debugger (Dart-Code's debugger)

For now this should setup `dap` so that once you have _already started a flutter app_. You should be able
to use `dap` commands to begin to debug it.

### TODO

- [ ] Add wrapper functions/commands around dab functionality to simplify.
- [ ] Investigate sporadic debugger crash.
