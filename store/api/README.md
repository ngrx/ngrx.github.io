# API Reference

Complete API reference for @ngrx/store.

### Services
* [Store](store.md)

### Interfaces
* [ActionReducer](action_reducer.md)
* [Action](action.md)

### Top-Level Exports

* [`provideStore(reducer: any, initialState?: any)`](provide_store.md)
* [`combineReducers(reducers: any): ActionReducer<any>`](combine_reducers.md)

### Store API

* [Store](store.md)
  * [`select: SelectSignature<T>)`](store.md#select)
  * [`dispatch(action : Action)`](store.md#dispatch)
  * [`replaceReducer(reducer: ActionReducer<any>)`](store.md#replaceReducer)