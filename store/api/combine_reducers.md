# combineReducers
### signature: `combineReducers(reducers: any): ActionReducer<any>`

Accepts an object map of reducer functions, returning a meta-reducer. The parent reducer will invoke each child reducer 
with the previous slice of state and action each time an action is dispatched.

> :bulb: More: [Reducer Composition](../recipes/reducers/reducer_composition.md)

#### Arguments

1. `reducers : any` - Object map of reducer functions. 

#### Returns
([*`ActionReducer`*](action_reducer.md)): ActionReducer.

#### Example

```ts
import { combineReducers } from '@ngrx/store';

export const reducer = combineReducers({
  router: routerReducer,
  search: searchReducer,
  books: booksReducer,
  collection: collectionReducer
})
```

> :file_folder: [https://github.com/ngrx/store/blob/master/src/utils.ts](https://github.com/ngrx/store/blob/master/src/utils.ts)
