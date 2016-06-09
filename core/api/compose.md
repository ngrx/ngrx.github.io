# compose
### signature: `compose: (...fns: any[]): (input: any) => any`

The compose function is one of our most handy tools. In basic terms, you give it any number of functions and it returns a function.
This new function takes a value and chains it through every composed function, returning the output.

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