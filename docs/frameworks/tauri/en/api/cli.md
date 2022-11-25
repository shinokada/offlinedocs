---
id: cli
title: CLI
---

import Command from '@theme/Command'

## `info`

<Command name=info />

```
Usage: cargo-tauri info [OPTIONS]

Options:
  -v, --verbose...  Enables verbose logging
  -h, --help        Print help information
  -V, --version     Print version information
```

It shows a concise list of information about the environment, Rust, Node.js and their versions as well as some relevant configurations.

</blockquote>info
This command is pretty helpful when you need to have a quick overview of your application. When requesting some help, it can be useful that you share this report with us.
</blockquote>

## `init`

<Command name=init />

```
Usage: cargo-tauri init [OPTIONS]

Options:
      --ci
          Skip prompting for values
  -v, --verbose...
          Enables verbose logging
  -f, --force
          Force init to overwrite the src-tauri folder
  -l, --log
          Enables logging
  -d, --directory <DIRECTORY>
          Set target directory for init [default: /home/runner/work/tauri-docs/tauri-docs]
  -t, --tauri-path <TAURI_PATH>
          Path of the Tauri project to use (relative to the cwd)
  -A, --app-name <APP_NAME>
          Name of your Tauri application
  -W, --window-title <WINDOW_TITLE>
          Window title of your Tauri application
  -D, --dist-dir <DIST_DIR>
          Web assets location, relative to <project-dir>/src-tauri
  -P, --dev-path <DEV_PATH>
          Url of your dev server
      --before-dev-command <BEFORE_DEV_COMMAND>
          A shell command to run before `tauri dev` kicks in
      --before-build-command <BEFORE_BUILD_COMMAND>
          A shell command to run before `tauri build` kicks in
  -h, --help
          Print help information
  -V, --version
          Print version information
```

## `plugin init`

<Command name=plugin init />

```
Usage: cargo-tauri plugin init [OPTIONS] --name <PLUGIN_NAME>

Options:
  -n, --name <PLUGIN_NAME>       Name of your Tauri plugin
  -v, --verbose...               Enables verbose logging
      --api                      Initializes a Tauri plugin with TypeScript API
  -d, --directory <DIRECTORY>    Set target directory for init [default: /home/runner/work/tauri-docs/tauri-docs]
  -t, --tauri-path <TAURI_PATH>  Path of the Tauri project to use (relative to the cwd)
  -a, --author <AUTHOR>          Author name
  -h, --help                     Print help information
  -V, --version                  Print version information
```

## `dev`

<Command name=dev />

```
Usage: cargo-tauri dev [OPTIONS] [ARGS]...

Arguments:
  [ARGS]...  Command line arguments passed to the runner. Arguments after `--` are passed to the application

Options:
  -r, --runner <RUNNER>           Binary to use to run the application
  -v, --verbose...                Enables verbose logging
  -t, --target <TARGET>           Target triple to build against
  -f, --features [<FEATURES>...]  List of cargo features to activate
  -e, --exit-on-panic             Exit on panic
  -c, --config <CONFIG>           JSON string or path to JSON file to merge with tauri.conf.json
      --release                   Run the code in release mode
      --no-watch                  Disable the file watcher
  -h, --help                      Print help information
  -V, --version                   Print version information
```

This command will open the WebView in development mode. It makes use of the `build.devPath` property from your `src-tauri/tauri.conf.json` file.

If you have entered a command to the `build.beforeDevCommand` property, this one will be executed before the `dev` command.

**[See more about the configuration.](./config.md.html#build)**

</blockquote>caution Troubleshooting
If you're not using `build.beforeDevCommand`, make sure your `build.devPath` is correct and, if using a development server, that it's started before using this command.
</blockquote>

## `build`

<Command name=build />

```
Usage: cargo-tauri build [OPTIONS] [ARGS]...

Arguments:
  [ARGS]...
          Command line arguments passed to the runner

Options:
  -r, --runner <RUNNER>
          Binary to use to build the application, defaults to `cargo`

  -v, --verbose...
          Enables verbose logging

  -d, --debug
          Builds with the debug flag

  -t, --target <TARGET>
          Target triple to build against.
          
          It must be one of the values outputted by `$rustc --print target-list` or `universal-apple-darwin` for an universal macOS application.
          
          Note that compiling an universal macOS application requires both `aarch64-apple-darwin` and `x86_64-apple-darwin` targets to be installed.



  -f, --features [<FEATURES>...]
          Space or comma separated list of features to activate

  -b, --bundles [<BUNDLES>...]
          Space or comma separated list of bundles to package.
          
          Each bundle must be one of `deb`, `appimage`, `msi`, `app` or `dmg` on MacOS and `updater` on all platforms. If `none` is specified, the bundler will be skipped.
          
          Note that the `updater` bundle is not automatically added so you must specify it if the updater is enabled.



  -c, --config <CONFIG>
          JSON string or path to JSON file to merge with tauri.conf.json

  -h, --help
          Print help information (use `-h` for a summary)

  -V, --version
          Print version information
```

This command will bundle your application, either in production mode or debug mode if you used the `--debug` flag. It makes use of the `build.distDir` property from your `src-tauri/tauri.conf.json` file.

If you have entered a command to the `build.beforeBuildCommand` property, this one will be executed before the `build` command.

**[See more about the configuration.](./config.md.html#build)**

## `icon`

<Command name=icon />

```
Usage: cargo-tauri icon [OPTIONS] [INPUT]

Arguments:
  [INPUT]  Path to the source icon (png, 1240x1240px with transparency) [default: ./app-icon.png]

Options:
  -o, --output <OUTPUT>  Output directory. Default: 'icons' directory next to the tauri.conf.json file
  -v, --verbose...       Enables verbose logging
  -h, --help             Print help information
  -V, --version          Print version information
```

[Tauri Icon Guide](../guides/features/icons.html)

## `version`

<Command name=--version />

```
  Description
    Returns the current version of tauri
```

This command will show the current version of Tauri.

## CLI usage

See more about the usage through this [complete guide](../guides/development/development-cycle.html).
<span style='float: footnote;'><a href="../index.html#toc">Go to TOC</a></span>
