# API Reference

Complete API reference for @ngrx/effects.

### Services
* [StateUpdates](state_updates.md)

### Interfaces
* [StateUpdate](state_update.md)

### Top-Level Exports
* [`Effect(): PropertyDecorator`](effect.md)
* [`mergeEffects(...instances: any[]): Observable<any>`](merge_effects.md)
* [`runEffects(...effects: any[])`](run_effects.md)
* [`toPayload(update: StateUpdate<any>): any`](to_payload.md)
* [`all(): boolean`](all.md)

### StateUpdates API
* [StateUpdates](state_updates.md)
    * [`whenAction(...actionTypes: string[]): Observable<StateUpdate<S>>`](state_updates.md#whenaction)