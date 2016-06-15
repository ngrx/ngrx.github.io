# mergeEffects
### signature: `mergeEffects(...instances: any[]): Observable<any>`

Helper function to automatically merge all decorated effects across any number of effect services.

#### Arguments

1. `...instances : any` - Variable number of services with registered effects. 

#### Returns
*`Observable<any>`* - Observable of merged effects

#### Example
```ts
import { OpaqueToken, Inject } from '@angular/core';
import { mergeEffects } from '@ngrx/effects';

const EFFECTS = new OpaqueToken('Effects');

@Component({
  providers: [
    provide(EFFECTS, { multi: true, useClass: AuthEffects }),
    provide(EFFECTS, { multi: true, useClass: AccountEffects }),
    provide(EFFECTS, { multi: true, useClass: UserEffects })
  ]
})
export class SomeCmp {
  constructor(@Inject(EFFECTS) effects: any[], store: Store<State>) {
    mergeEffects(effects).subscribe(store);
  }
}
```

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/effects.ts](https://github.com/ngrx/effects/blob/master/lib/effects.ts)