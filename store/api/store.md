# Store

The store holds the entire state tree for your application. There will be one store instance per application.  

## Methods

- [`select<T, R>(pathOrMapFn: any, ...paths: string[]): Observable<R>`](#select)
- [`dispatch(action : Action)`](#dispatch)
- [`replaceReducer(reducer: ActionReducer<any>)`](#replacereducer)


### <a id='select'></a>[`select<T, R>(pathOrMapFn: any, ...paths: string[]): Observable<R>`](#select)

Select slice of state based on supplied value. If a function or single string is supplied, `select` will call `map`
and `distinctUntilChanged`. If multiple strings are supplied `select` will call `pluck` and `distinctUntilChanged`, each returning the correct state slice.

#### Arguments

1. `pathOrMapFn: any, ...paths: string[]` - Function, string (or string[]), number, or symbol representing the appropriate slice of state to be returned.

#### Returns

*(Observable<R>)*: Observable of selected state.

#### Example

###### Selecting Section of State
```ts
export class Todos {
    constructor(
            private store : Store<AppState>
        ){
            //using string
            store.select<Todos[]>('todos');
            //using Function
            store.select<Todos[]>(state => state.todos);
            //nested selection
            store.select<boolean>('orders', 'ingredients');
            
        }
}
```

###### Within a Selector
```ts
export function getBooksState() {
  return (state$: Observable<AppState>) => state$
    .select(s => s.books);
}
```

<hr>

### <a id='dispatch'></a>[`dispatch(action : Action)`](#dispatch)

Dispatches an action into store.

#### Arguments

1. `action : Action` - Action to be dispatched.

#### Returns

*(void)*

#### Example

###### Dispatching Basic Action
```ts
export class Counter{
    constructor(
        private store : Store<number>
    ){
        //select counter
    }
    //dispatch basic action
    increment() {
        this.store.dispatch({type: 'INCREMENT'})
    }  
}
```

######Dispatching Action With Payload
```ts
export class Todos {
    constructor(
            private _store : Store<AppState>
        ){
            //select todos
        }
        //dispatching action with payload
        addTodo(description : string){
            this._store.dispatch({type: ADD_TODO, payload: {
                id: ++this.id,
                description,
                complete: false
            }});
        }
}
```

<hr>

### <a id='replaceReducer'></a>[`replaceReducer(reducer: ActionReducer<any>)`](#replacereducer)

Replaces specified reducer. 

#### Arguments

1. `reducer` (*ActionReducer<V>*) The reducer to replace current reducer.

#### Returns

*(void)*

> :file_folder: [https://github.com/ngrx/store/blob/master/src/store.ts](https://github.com/ngrx/store/blob/master/src/store.ts)
