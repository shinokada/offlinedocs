
# pnpm deploy


Added in: v7.4.0

Deploy a package from a workspace.

Usage:

```
pnpm --filter=<deployed project name> deploy <target directory>
```

In case you build your project before deployment, also use the `--prod` option to skip `devDependencies` installation.

```
pnpm --filter=<deployed project name> --prod deploy <target directory>
```

Usage in a docker image. After building everything in your monorepo, do this in a second image that uses your monorepo base image as a build context or in an additional build stage:

```Dockerfile
# syntax=docker/dockerfile:1.4

FROM workspace as pruned
RUN pnpm --filter <your package name> --prod deploy pruned

FROM node:18-alpine
WORKDIR /app

ENV NODE_ENV=production

COPY --from=pruned /app/pruned .

ENTRYPOINT [node, index.js]
```

## Files included in the deployed project

By default, all the files of the project are copied during deployment. The project's `package.json` may contain a files field to list the files and directories that should be copied.

<span style='float: footnote;'><a href="../index.html#toc">Go to TOC</a></span>
