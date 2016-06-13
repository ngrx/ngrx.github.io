# compose
### signature: `compose: (...fns: any[]): (input: any) => any`

Compose accepts any number of functions and returns a function.
This new function takes a value and chains it through every composed function, right to left, returning the output.

#### Arguments

1. `...functions : any[]` - variable number of functions. 

#### Returns
*`(input: any) => any`*

#### Example - Basic Compose
```ts
const add = numOne => numTwo => numOne + numTwo;
const example = compose(add(10), add(20));
example(5) //35
```

#### Example - Compose With Selectors
```ts
 export function getBooksState() {
  return (state$: Observable<AppState>) => state$
    .select(s => s.books);
}

 export function getBookEntities() {
   return compose(fromBooks.getBookEntities(), getBooksState());
 }
```

#### Example - Compose With Reducers
```ts
export default compose(storeLogger(), combineReducers)({
  router: routerReducer,
  search: searchReducer,
  books: booksReducer,
  collection: collectionReducer
});
```

> :file_folder: [https://github.com/ngrx/core/blob/master/lib/compose.ts](https://github.com/ngrx/core/blob/master/lib/compose.ts)