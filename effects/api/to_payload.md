# toPayload
### signature: `toPayload(update: StateUpdate<any>): any`

Helper function which accepts a `StateUpdate` object, returning just the action payload. This is useful in situations
you are only concerned with the dispatched action payload, not the current state.

#### Arguments

1. `update : StateUpdate<any>` - State update from store 

#### Returns
*`any`* - Payload for last dispatched action

#### Example
```ts
@Effect() search$ = this.updates$
    .whenAction(BookActions.SEARCH)
    .map<string>(toPayload)
    .filter(query => query !== '')
    .switchMap(query => this.googleBooks.searchBooks(query)
      .map(books => this.bookActions.searchComplete(books))
      .catch(() => Observable.of(this.bookActions.searchComplete([])))
    );
```

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/util.ts](https://github.com/ngrx/effects/blob/master/lib/util.ts)