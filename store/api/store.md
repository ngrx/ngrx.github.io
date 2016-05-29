# Store

The store holds the entire state tree for your application. There will be one store instance per application.  

### Store Methods

- [select<T, R>(pathOrMapFn: any, ...paths: string[]): Observable<R>)](#select)
- [dispatch(action : Action)](#dispatch)
- [replaceReducer(reducer: ActionReducer<any>)](#replacereducer)

## Store Methods

### <a id='select'></a>[`select<T, R>(pathOrMapFn: any, ...paths: string[]): Observable<R>`](#select)

Select slice of state based on supplied value. 

#### Arguments

1. `pathOrMapFn: any, ...paths: string[]` - Function, string (or string[]), number, or symbol representing the appropriate slice of state to be returned.

#### Returns

*(Observable<R>)*: Observable of selected state.

<hr>

### <a id='dispatch'></a>[`dispatch(action : Action)`](#dispatch)

Dispatches an action. This is equivalent to calling `dispatcher.dispatch`.

#### Arguments

1. `action : Action` - Action to be dispatched.

#### Returns

*(void)*

<hr>

### <a id='replaceReducer'></a>[`replaceReducer(reducer: ActionReducer<any>)`](#replacereducer)

Replaces specified reducer. 

#### Arguments

1. `reducer` (*ActionReducer<V>*) The reducer to replace current reducer.

#### Returns

*(void)*
