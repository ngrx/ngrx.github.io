# combineReducers
### signature: `combineReducers(reducers: any): ActionReducer<any>`

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