
#### Implementing a Meta-Reducer

([Work Along](https://plnkr.co/edit/4wFpmsCpDhGW6h1WopyC?p=preview) | [Completed Lesson](https://plnkr.co/edit/Hk77IPgofglnwQjF971E?p=preview))

One of the many advantages to a single, immutable state tree is the ease of implementation for generally tricky features like undo/redo. Since the progression of application state is fully tracable through snapshots of store, the ability to walk back through these snap shots becomes trivial. A popular method for implementing this functionality is through *meta-reducers*.

Despite the ominous sound, *meta-reducers* are actually quite simple in theory and implementation. To create a meta-reducer, you wrap a current reducer in a parent reducer, delegating the majority of actions through the wrapped reducer as normal, stepping in only when defined *meta* actions (such as undo/redo) are dispatched. 
How does this look in practice? Let's see by creating a reset feature for our party planning application, allowing the user to *start from scratch* if they want to enter all new data. 

To encapsulate this functionality we create a factory function, accepting any reducer to wrap, returning our `reset` reducer. When the reset reducer is initialize we grab the initial state of the wrapped reducer, saving it for later use. All that is left is to listen for a specific reset action to be dispatched. If `RESET_STATE` is not dispatched, the action is passed to the wrapped reducer and state returned as normal. When `RESET_STATE` is triggered the stored initial state is returned instead of the result of invoking the wrapped reducer. Undo/redo can be handled similarly, keeping track of previous actions in local state. 

###### Reset Meta-Reducer
```ts
export const RESET_STATE = 'RESET_STATE';

const INIT = '__NOT_A_REAL_ACTION__';

export const reset = reducer => {
    let initialState = reducer(undefined, {type: INIT})
    return function (state, action) {
      //if reset action is fired, return initial state
      if(action.type === RESET_STATE){
        return initialState;
      }
      //calculate next state based on action
      let nextState = reducer(state, action);
      //return nextState as normal when not reset action
      return nextState;
  }
} 
```

###### Wrap Reducer On Bootstrap
```ts
bootstrap(App, [
  provideStore({people: reset(people), partyFilter})
]);
```

It is worth noting that the store *root reducer* is itself a meta-reducer, created behind the scenes when calling `provideStore` (by <sup>14</sup> `combineReducers`). For each dispatched action, the root reducer invokes each application reducer with the previous state and current action, returning an object map of `[reducer]: state[reducer]`. 

###### <sup>14</sup> combineReducers
```ts
export function combineReducers(reducers: any): Reducer<any> {
  const reducerKeys = Object.keys(reducers);
  const finalReducers = {};

  for (let i = 0; i < reducerKeys.length; i++) {
    const key = reducerKeys[i];
    if (typeof reducers[key] === 'function') {
      finalReducers[key] = reducers[key];
    }
  }

  const finalReducerKeys = Object.keys(finalReducers);

  return function combination(state = {}, action) {
    let hasChanged = false;
    const nextState = {};
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i];
      const reducer = finalReducers[key];
      const previousStateForKey = state[key];
      const nextStateForKey = reducer(previousStateForKey, action);

      nextState[key] = nextStateForKey;
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey;
    }
    return hasChanged ? nextState : state;
  };
}
```