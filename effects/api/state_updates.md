# StateUpdates

## Methods

- [`whenAction(...actionTypes: string[]): Observable<StateUpdate<S>>): Observable<StateUpdate<S>>`](#whenaction)

<hr>

### <a id='whenaction'></a>[`whenAction(...actionTypes: string[]): Observable<StateUpdate<S>>`](#whenaction)

Filters out all but the supplied action or group of actions dispatched through store.

#### Arguments

1. `...actionTypes : string[]` - Actions for the effect to listen.

#### Returns

*Observable<StateUpdate<S>>*

#### Example

###### Add Collection Effect
```ts
@Effect() addBookToCollection$ = this.updates$
    .whenAction(BookActions.ADD_TO_COLLECTION)
    .map<Book>(toPayload)
    .mergeMap(book => this.db.insert('books', [ book ])
      .mapTo(this.bookActions.addToCollectionSuccess(book))
      .catch(() => Observable.of(
        this.bookActions.removeFromCollectionFail(book)
      ))
    );
```

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/state-updates.ts](https://github.com/ngrx/effects/blob/master/lib/state-updates.ts)

