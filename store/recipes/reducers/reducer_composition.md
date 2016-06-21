# Reducer Composition
@ngrx/store and redux applications are built on the concept of **reducer composition**, or a parent reducer invoking child reducers
to ultimately output a new representation of application state. This composition either occurs explicitly when using the [`combineReducers`](../../api/combine_reducers.md) helper function, or 
behind the scenes when invoking [`provideStore`](../../api/provide_store.md) with an object map of reducer functions at application bootstrap. In the latter case, `combineReducers` is invoked for 
you when the store is created. Let's take a look at the @ngrx/store `combineReducers` implementation.

###### combineReducers
```ts
export function combineReducers(reducers: any): ActionReducer<any> {
  const reducerKeys = Object.keys(reducers);
  const finalReducers = {};

  for (let i = 0; i < reducerKeys.length; i++) {
    const key = reducerKeys[i];
    if (typeof reducers[key] === 'function') {
      finalReducers[key] = reducers[key];
    }
  }

  const finalReducerKeys = Object.keys(finalReducers);

  return function combination(state = {}, action) {
    let hasChanged = false;
    const nextState = {};
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i];
      const reducer = finalReducers[key];
      const previousStateForKey = state[key];
      const nextStateForKey = reducer(previousStateForKey, action);

      nextState[key] = nextStateForKey;
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey;
    }
    return hasChanged ? nextState : state;
  };
}
```

As seen above, `combineReducers` takes an object map of reducer functions, returning a parent, or meta-reducer.
This reducer is invoked for each dispatched action, subsequently invoking all child reducers with the previous state for that key, and current action.

While reducer composition is most often seen during the creation of the store, you can also utilize `combineReducers` as a utility
to keep your reducers small and focused. Let's take a look at an example of splitting one reducer into multiple, achieving the
same result using reducer composition.

The [@ngrx/example-app](https://github.com/ngrx/example-app) features a book reducer which stores the collection of books loaded into the application. The books reducer outputs
the following state shape:

######BooksState
```ts
 export interface BooksState {
  ids: string[];
  entities: { [id: string]: Book };
};
```

Each time a book, or collection of books is loaded the id(s) are stored in an array, while the book entities are maintained in an 
`{ [id: string]: Book }` object map. Both of these properties are currently populated within the same reducer. 


###### Book Reducer - Original
```ts
export default function(state = initialState, action: Action): BooksState {
  switch (action.type) {
    case BookActions.SEARCH_COMPLETE:
    case BookActions.LOAD_COLLECTION_SUCCESS: {
      const books: Book[] = action.payload;
      const newBooks = books.filter(book => !state.entities[book.id]);

      const newBookIds = newBooks.map(book => book.id);
      const newBookEntities = newBooks.reduce((entities: { [id: string]: Book }, book: Book) => {
        return Object.assign(entities, {
          [book.id]: book
        });
      }, {});

      return {
        ids: [ ...state.ids, ...newBookIds ],
        entities: Object.assign({}, state.entities, newBookEntities)
      };
    }

    case BookActions.LOAD_BOOK: {
      const book: Book = action.payload;

      if (state.ids.includes(book.id)) {
        return state;
      }

      return {
        ids: [ ...state.ids, book.id ],
        entities: Object.assign({}, state.entities, {
          [book.id]: book
        })
      };
    }

    default: {
      return state;
    }
  }
}
```
Let's see how we can utilize reducer composition in order to split these sections of book state into their own child reducers
to be invoked by a single parent reducer. 
First, we'll extract the ids array into it's own reducer.

> :bulb: Tip: Having your reducers backed by a suite of unit tests makes refactoring your reducers stress free, just follow the green light! 
For more, check out the [testing reducers](testing.md) section.

###### ids reducer
```ts
const ids = (state = [], action : Action) : string[] => {
  switch (action.type){
    case BookActions.SEARCH_COMPLETE:
    case BookActions.LOAD_COLLECTION_SUCCESS: {
      const books: Book[] = action.payload;
      const bookIds: string[] = books.map(book => book.id);
      return [
        ...state,
        ...bookIds.filter(id => !state.includes(id))
      ]
    }

    case BookActions.LOAD_BOOK: {
      const book: Book = action.payload;
      if(state.includes(book.id)){
        return state;
      }
      return [...state, book.id];
    }
    default:
      return state;
  }
}
```
Next, we can extract an `entities` reducer, focused on outputting the object map of id's to books. Notice that each
reducer is focused on a single task.

###### entities reducer
```ts
const entities = (state : { [id: string]: Book } = {}, action : Action) : { [id: string]: Book } => {
  switch(action.type){
    case BookActions.SEARCH_COMPLETE:
    case BookActions.LOAD_COLLECTION_SUCCESS: {
      const books: Book[] = action.payload;
      const newBooks = books.filter(book => !state[book.id]);
      const newBookEntities = newBooks.reduce((entities: { [id: string]: Book }, book: Book) => {
        return Object.assign(entities, {
          [book.id]: book
        });
      }, {});

      return Object.assign({}, state, newBookEntities)
    }
    case BookActions.LOAD_BOOK: {
      const book: Book = action.payload;

      if (state[book.id]) {
        return state;
      }

      return Object.assign({}, state, {
          [book.id]: book
      })     
    }

    default:
      return state;
  }
};
```

Finally, utilizing the `combineReducers` function we can create the parent books reducer which is a composition of the 
`ids` and `entities` child reducers. This will be exported and registered with the `provideStore` function, the same as the books reducer before.

###### combineReducers for Books
```ts
const books : ActionReducer<BooksState> = combineReducers({
  ids,
  entities
});

export default books;
```

To recap, reducer composition is the heart of any redux or @ngrx/store style application. Utilize the `combineReducers` helper function
aggressively to keep your reducers small, focused, and easy to reason about!

**Next: [Testing Reducers](testing.md)**