# runEffects
### signature: `runEffects(...effects: any[])`

#### Example
```ts
import { runEffects } from '@ngrx/effects';

bootstrap(App, [
  provideStore(reducer),
  runEffects(AuthEffects)
]);
```

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/run-effects.ts](https://github.com/ngrx/effects/blob/master/lib/run-effects.ts)