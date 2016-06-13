# StateUpdate
### signature: `StateUpdate<S> {state: S; action: Action;}`

A `StateUpdate` is emitted from the `StateUpdates` observable service each time your state updates.
Each `StateUpdate` includes the current state and last dispatched action.

> :file_folder: [https://github.com/ngrx/effects/blob/master/lib/state-updates.ts](https://github.com/ngrx/effects/blob/master/lib/state-updates.ts)
