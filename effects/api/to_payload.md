# toPayload
### signature: `toPayload(update: StateUpdate<any>): any`

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