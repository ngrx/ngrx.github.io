# API Reference

Complete API reference for @ngrx/effects.

### Services
* [StateUpdates](state_updates.md)

### Types
* [StateUpdate](state_update.md)

### Decorators
* [`Effect(): PropertyDecorator`](effect.md)

### Utility
* [`runEffects(...effects: any[])`](run_effects.md)
* [`mergeEffects(...instances: any[])`](merge_effects.md)
* [`toPayload(update: StateUpdate<any>): any`](to_payload.md)

### StateUpdates API
* [StateUpdates](state_updates.md)
    * [`whenAction(...actionTypes: string[]): Observable<StateUpdate<S>>`](state_updates.md#whenaction)