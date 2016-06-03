# provideStore
### signature: `provideStore(reducer: any, initialState?: any)`

#### Arguments

1. `reducer : ActionReducer | [key:string]: ActionReducer` - Reducer or map of reducer functions. If multiple reducers are supplied reducers are 
automatically combined to create root reducer.

2. `initialState? : any` : Initial state for given reducer(s). This can be useful if supplying initial state from another service, 
or when using store in universal applications.

#### Returns
([*`Store`*](store.md)): Application store.

#### Example
```ts
import { bootstrap } from '@angular/platform-browser-dynamic';
import { provideStore } from '@ngrx/store';
import { App } from './myapp';
import { counterReducer } from './counter';

bootstrap(App, [
	provideStore({ counter: counterReducer })
]);
```

> :file_folder: [https://github.com/ngrx/store/blob/master/src/ng2.ts](https://github.com/ngrx/store/blob/master/src/ng2.ts)