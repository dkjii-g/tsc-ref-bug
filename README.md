`tsc --build repo2`

`repo1/dist/a.d.ts(1,19): error TS2307: Cannot find module 'b' or its corresponding type declarations.`

`tsc --build repo1`

`success`

Type declaration imports are not resolved correctly.
They are resolved from the rules in `repo2/tsconfig.json`.

This means that if we create repo2/b.ts with this content:
```
import {T} from 'repo1';

export type {T};
```

This is the content for repo3.

`tsc --build repo3`

`repo3/b.ts(1,9): error TS2303: Circular definition of import alias 'T'.`

since `repo1/a.d.ts` imports 'b' (resolves to `repo3/b.ts`) -> imports `repo1/a.d.ts` -> ...

`repo1/a.d.ts` importing 'b' should resolve to `repo1/b.d.ts` and not `repo3/b.ts`

To show that this is an issue with d.ts file importing and no path/references, `repo4`
has no tsconfig and has a copy of repo1 inside its node_modules.
