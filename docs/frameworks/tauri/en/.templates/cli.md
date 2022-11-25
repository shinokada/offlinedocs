---
id: cli
title: CLI
---

import Command from '@theme/Command'

## `info`

<Command name=info />

{info}

It shows a concise list of information about the environment, Rust, Node.js and their versions as well as some relevant configurations.

</blockquote>info
This command is pretty helpful when you need to have a quick overview of your application. When requesting some help, it can be useful that you share this report with us.
</blockquote>

## `init`

<Command name=init />

{init}

## `plugin init`

<Command name=plugin init />

{plugin init}

## `dev`

<Command name=dev />

{dev}

This command will open the WebView in development mode. It makes use of the `build.devPath` property from your `src-tauri/tauri.conf.json` file.

If you have entered a command to the `build.beforeDevCommand` property, this one will be executed before the `dev` command.

**[See more about the configuration.](./config.md.html#build)**

</blockquote>caution Troubleshooting
If you're not using `build.beforeDevCommand`, make sure your `build.devPath` is correct and, if using a development server, that it's started before using this command.
</blockquote>

## `build`

<Command name=build />

{build}

This command will bundle your application, either in production mode or debug mode if you used the `--debug` flag. It makes use of the `build.distDir` property from your `src-tauri/tauri.conf.json` file.

If you have entered a command to the `build.beforeBuildCommand` property, this one will be executed before the `build` command.

**[See more about the configuration.](./config.md.html#build)**

## `icon`

<Command name=icon />

{icon}

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
