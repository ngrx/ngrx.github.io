# Core Concepts
Let's first take a high-level look at the core concepts of @ngrx/store. Each application built around store will contain three main pieces, __reducers__, __actions__, and a __single application store__. Let’s take a moment to explore each of these concepts.

##### Store
Like a traditional database represents the point of record for an application, your __store__ can be thought of as a client side _‘single source of truth’_, or database. By adhering to the <sup>1</sup> *store contract* when designing your application, a snapshot of store at any point will supply a complete representation of relevant application state. This becomes extremely powerful when it comes to reasoning about user interaction, debugging, and in the context of Angular 2, performance.

<sup>1 Single, immutable state tree updated only through explicitly defined and dispatched actions</sup>
###### Centralized, Immutable State
![store data-flow](http://imgur.com/zLtnV7p.png)

##### Reducers
The second integral part of a store application is __reducers__. A <sup>2</sup> reducer is a <sup>3</sup> pure function, accepting two arguments, the previous state and an action with a type and optional data (payload) associated with the event. Using the previous analogy, if store is to be thought of as your client side database, reducers can be considered the tables in said database. Reducers represent sections, or slices of state within your application and should be structured and composed accordingly.

###### <sup>2</sup> Reducer Interface
```ts
export interface Reducer<T> {
  (state: T, action: Action): T;
}
```
<sup>3 A function whose return value is determined only by its input values, with no observable side-effects.</sup>

###### Sample Reducer
```ts
export const counter: Reducer<number> = (state: number = 0, action: Action) => {
    switch(action.type){
        case 'INCREMENT':
            return state + 1;
        case 'DECREMENT':
            return state - 1;
        default:
            return state;
    }
};
```


##### Actions
Store encompasses our application state and reducers output sections of that state, but how do we communicate to our reducers when state needs to be updated? That is the role of <sup>4</sup> __actions__. Within a store application, all user interaction that would cause a state update must be expressed in the form of actions. All relevant user events are dispatched as actions, flowing through your registered reducer functions, before a new representation of state is output. This process occurs each time an action is dispatched, leaving a complete, serializable representation of application state changes over time.
###### <sup>4</sup> Action Interface
```ts
export interface Action {
  type: string;
  payload?: any;
}
```
###### Sample Actions
```ts
//simple action without a payload
dispatch({type: 'DECREMENT'});

//action with an associated payload
dispatch({type: ADD_TODO, payload: {id: 1, message: 'Learn ngrx/store', completed: true}})
```
##### Projecting Data
Finally, we need to extract, combine, and project data from store for display in our views. Because store itself is an observable, we have access to the typical JS collection operations you are accustom to (map, filter, reduce, etc.) along with a wide-array of extremely powerful RxJS based observable operators. This makes slicing up store data into any projection you wish quite easy.

###### State Projection
```ts
//most basic example, get people from state
store.select('people')
  
//combine multiple state slices
Observable.combineLatest(
  store.select('people'),
  store.select('events'),
  (people, events) => {
    //projection here
})
```