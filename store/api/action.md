# Action
### signature: `{ type: string; payload?: any;  }`
Actions are the only method of communication between your application and store. In __@ngrx/store__, actions require a `type` and an optional `payload`, or information carried by the action. Your actions should create a clear history of user interaction within your application.

####Example - Dispatching Basic Actions
```ts
//dispatching an action without a payload
this.store.dispatch({ type: 'INCREMENT' });

//dispatching an action with a payload
this.store.dispatch({ type: 'ADD_USER', payload: {name: 'Joe'} });
```

#### Example - Subscribe Store to Action Emitting Stream
```
public action$: Subject<Action> = new Subject<Action>();
//later...
this.actions$.subscribe(store)
```

> :file_folder: [https://github.com/ngrx/store/blob/master/src/dispatcher.ts](https://github.com/ngrx/store/blob/master/src/dispatcher.ts)

