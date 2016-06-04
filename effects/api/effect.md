# Effect
### signature: `Effect(): PropertyDecorator`

#### Example - Authentication Effect
```ts
@Injectable()
export class AuthEffects {
  constructor(private http: Http, private updates$: StateUpdates<any>) { }

  @Effect() login$ = this.updates$
      // Listen for the 'LOGIN' action
      .whenAction('LOGIN')
      // Map the payload into JSON to use as the request body
      .map(update => JSON.stringify(update.action.payload))
      .switchMap(payload => this.http.post('/auth', payload)
        // If successful, dispatch success action with result
        .map(res => ({ type: 'LOGIN_SUCCESS', payload: res.json() }))
        // If request fails, dispatch failed action
        .catch(() => Observable.of({ type: 'LOGIN_FAILED' }));
      );
}
```

#### Example - Search Effect
```ts
@Injectable()
export class BookEffects {
  constructor(
    private updates$: StateUpdates<AppState>,
    private googleBooks: GoogleBooksService,
    private db: Database,
    private bookActions: BookActions
  ) { }
  
@Effect() search$ = this.updates$
    .whenAction(BookActions.SEARCH)
    .map<string>(toPayload)
    .filter(query => query !== '')
    .switchMap(query => this.googleBooks.searchBooks(query)
      .map(books => this.bookActions.searchComplete(books))
      .catch(() => Observable.of(this.bookActions.searchComplete([])))
    );
```

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/metadata.ts](https://github.com/ngrx/effects/blob/master/lib/metadata.ts)