# Configure KaTeX

<Environment type="node" />

Create `./setup/katex.ts` with the following content:

```ts
import { defineKatexSetup } from '@slidev/types'

export default defineKatexSetup(() => {
  return {
    /* ... */
  }
})
```

With the setup, you can provide the custom setting for [KaTex Options](https://katex.org/docs/options.html). Refer to the type definitions and their documentation for more details.
<span style='float: footnote;'><a href="../index.html#toc">Go to TOC</a></span>
