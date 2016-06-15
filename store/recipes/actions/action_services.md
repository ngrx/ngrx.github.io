# Action Services

 Instead of passing around action string constants and manually recreating
 action objects at the point of dispatch, we create services encapsulating
 each appropriate action group. Action types are included as static
 members and stored next to their action creator. This promotes a
 uniform interface and single import for appropriate actions
 within your application components.
 

###### Action Creator Service

```ts
@Injectable()
export class BookActions {
  static SEARCH = '[Book] Search';
  search(query: string): Action {
    return {
      type: BookActions.SEARCH,
      payload: query
    };
  }

  static SEARCH_COMPLETE = '[Book] Search Complete';
  searchComplete(results: Book[]): Action {
    return {
      type: BookActions.SEARCH_COMPLETE,
      payload: results
    };
  }

  static ADD_TO_COLLECTION = '[Book] Add to Collection';
  addToCollection(book: Book): Action {
    return {
      type: BookActions.ADD_TO_COLLECTION,
      payload: book
    };
  }

  static ADD_TO_COLLECTION_SUCCESS = '[Book] Add to Collection Success';
  addToCollectionSuccess(book: Book): Action {
    return {
      type: BookActions.ADD_TO_COLLECTION_SUCCESS,
      payload: book
    };
  }

  static ADD_TO_COLLECTION_FAIL = '[Book] Add to Collection Fail';
  addToCollectionFail(book: Book): Action {
    return {
      type: BookActions.ADD_TO_COLLECTION_FAIL,
      payload: book
    };
  }

  static REMOVE_FROM_COLLECTION = '[Book] Remove from Collection';
  removeFromCollection(book: Book): Action {
    return {
      type: BookActions.REMOVE_FROM_COLLECTION,
      payload: book
    };
  }

  static REMOVE_FROM_COLLECTION_SUCCESS = '[Book] Remove From Collection Success';
  removeFromCollectionSuccess(book: Book): Action {
    return {
      type: BookActions.REMOVE_FROM_COLLECTION_SUCCESS,
      payload: book
    };
  }

  static REMOVE_FROM_COLLECTION_FAIL = '[Book] Remove From Collection Fail';
  removeFromCollectionFail(book: Book): Action {
    return {
      type: BookActions.REMOVE_FROM_COLLECTION_FAIL,
      payload: book
    };
  }
}
```

> :file_folder: [https://github.com/ngrx/example-app/blob/master/src/actions/book.ts](https://github.com/ngrx/example-app/blob/master/src/actions/book.ts)