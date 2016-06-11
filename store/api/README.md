# API Reference

Complete API reference for @ngrx/store.

### Services
* [Store](store.md)

### Types
* [ActionReducer](action_reducer.md)
* [Action](action.md)

### Functions

* [`provideStore(reducer: any, initialState?: any)`](provide_store.md)
* [`combineReducers(reducers: any): ActionReducer<any>`](combine_reducers.md)

### Store API

* [Store](store.md)
  * [`select: SelectSignature<T>)`](store.md#select)
  * [`dispatch(action : Action)`](store.md#dispatch)
  * [`replaceReducer(reducer: ActionReducer<any>)`](store.md#replaceReducer)