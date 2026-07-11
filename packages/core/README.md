# @datahogo/core

The scan engine behind [Data Hogo](https://github.com/datahogo/datahogo) — 300+ security checks across JS/TS, Python, Go, Java, PHP, C#, mobile, and Supabase. Runs entirely locally.

Most people should use the [`datahogo` CLI](https://www.npmjs.com/package/datahogo) instead of this package directly. Use `@datahogo/core` if you're building your own tool on top of the engine.

```ts
import { runScan } from "@datahogo/core";

const files = new Map([["src/app.ts", "..."]]);
const result = await runScan({ files });
console.log(result.score, result.findings);
```

Full docs: [github.com/datahogo/datahogo](https://github.com/datahogo/datahogo)

License: AGPL-3.0
