# runEffects
### signature: `runEffects(...effects: any[])`

Accepts an array of services with registered `[Effects](effect.md)`. `runEffects` configures all providers for @ngrx/effects. 
Observables decorated as an @Effect() within the supplied services will ultimately be merged, with emitted actions being dispatched into your application store.

#### Arguments

1. `...effects : any` - Variable number of services with registered effects. 

#### Returns
*`Provider[]`* - Effects providers

#### Example
```ts
import { runEffects } from '@ngrx/effects';

bootstrap(App, [
  provideStore(reducer),
  runEffects(AuthEffects)
]);
```

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/run-effects.ts](https://github.com/ngrx/effects/blob/master/lib/run-effects.ts)