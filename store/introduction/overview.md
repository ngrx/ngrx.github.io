# Overview
With the advent of Angular 2 new patterns, best practices, and libraries empowered by new framework features and functionality are emerging. One such library, championed by Angular developer advocate [Rob Wormald](https://twitter.com/robwormald) (huge props to [@MikeRyan52](https://twitter.com/mikeryan52) as well), is [@ngrx/store](https://github.com/ngrx/store). Store builds on the concepts made popular by [Redux](https://github.com/reactjs/redux), a popular state management container in the React community, and supercharges it with the backing of [RxJS](https://github.com/ReactiveX/rxjs). The result is a tool and philosophy that will revolutionize your applications and development experience. 


#### Core Concepts
Before we dive into the nuts and bolts of what makes store work, let's first take a high-level look at the core concepts. Each application built around store will contain three main pieces, __reducers__, __actions__, and a __single application store__. Let’s take a moment to explore each of these concepts.

##### Store
Like a traditional database represents the point of record for an application, your __store__ can be thought of as a client side _‘single source of truth’_, or database. By adhering to the <sup>1</sup> *store contract* when designing your application, a snapshot of store at any point will supply a complete representation of relevant application state. This becomes extremely powerful when it comes to reasoning about user interaction, debugging, and in the context of Angular 2, performance.

<sup>1 Single, immutable state tree updated only through explicitly defined and dispatched actions</sup>
###### Centralized, Immutable State
![store data-flow](http://imgur.com/g5AvQWq.png)

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
#### Not Your Classic Angular
In the previous section I mentioned _abiding by the store contract_ when developing your application. What exactly does this mean in the context of the typical Angular setup and workflow which you have grown accustomed? Let's take a look. 

If you are coming from an Angular 1 background you are familiar with <sup>5</sup> two-way data binding. The controller model binds to the view and vice versa. The problem with this approach presents itself as your view becomes more complex, requiring controllers and directives to manage and represent significant state changes over time. This can quickly turn into a nightmare both to reason about and debug as one change effects another, which effects another, and so on.

Store promotes the idea of <sup>6</sup> one-way data flow and explicitly dispatched actions. All state updates are handled above your components in store, delegated to reducers. The only way to initiate a state update in your application is through dispatched actions, corresponding to a particular reducer case. This not only makes reasoning about state changes in your application easier, as updates are centralized, it leaves a clear audit trail in case of error. 
###### <sup>5</sup> Two-Way Data Binding
![counter two-way](http://imgur.com/JUimvDf.png)
###### <sup>6</sup> One Way Data-Flow
![counter one-way](http://imgur.com/aAA74lS.png)
###### Non-Store Counter Example
([demo](https://gist.run/?id=f03ca24d7288835107f5b32f0274b44c))
```ts
@Component({
    selector: 'counter',
    template: `
    <div class="content">
        <button (click)="increment()">+</button>
        <button (click)="decrement()">-</button>
        <h3>{{counter}}</h3>
    </div>
    `
})
export class Counter{
    counter = 0;

    increment(){
        this.counter += 1;
    }

    decrement(){
        this.counter -= 1;
    }
}
```
###### Store Counter Example
([demo](https://gist.run/?id=07ab5be5ec83fa98ea724bc3cf2d5f4b))
```ts
@Component({
    selector: 'counter',
    template: `
    <div class="content">
        <button (click)="increment()">+</button>
        <button (click)="decrement()">-</button>
        <h3>{{counter$ | async}}</h3>
    </div>
    `,
    pipes: [AsyncPipe],
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class Counter{
    counter$: Observable<number>;

    constructor(
        private store : Store<number>
    ){
        this.counter$ = this.store.select('counter')
    }

    increment(){
        this.store.dispatch({type: 'INCREMENT'});
    }

    decrement(){
        this.store.dispatch({type: 'DECREMENT'});
    }
}
```
#### Advantages of Store
Throughout the overview we touched briefly on the advantages of utilizing Store over a typical, Angular 1 style approach but let's take a moment to recap. Why take the time to invest in this particular library, architecture pattern, and learning curve? The primary advantage to a Store-based application are __centralized state__, __performance__, __testability__, and __tooling__.
##### Centralized, Immutable State
All relevant application state exists in one location. This makes it easier to track down problems, as a snapshot of state at the time of an error can provide important insight and make it easy to recreate issues. This also makes notoriously hard problems such as undo/redo trivial in the context of a Store application and enables powerful tooling.
##### Performance
Since state is centralized at the top of your application, data updates can flow down through your components relying on slices of store. Angular 2 is built to optimize on such a data-flow arrangement, and can disable change detection in cases where components rely on Observables which have not emitted new values. In an optimal store solution this will be the vast majority of your components.
##### Testability
All state updates are handled in reducers, which are pure functions. Pure functions are extremely simple to test, as it is simply input in, assert against output. This enables the testing of the most crucial aspects of your application without mocks, spies, or other tricks that can make testing both complex and error prone. 
##### Tooling and Ecosystem
A centralized, immutable state also enables powerful tooling. One such example is [ngrx developer tools](https://github.com/ngrx/devtools), which provides a history of actions and state changes, allowing for <sup>7</sup> *time travel* during development. 
The patterns provided by Store also allow for a rich ecosystem of easy to implement middleware. Because store provides an entry point both before and after dispatched actions hit application reducers, problems such as syncing slices of state to local storage, advanced logging, and implementing sagas are easily solved with a quick package include and a few lines of code. This ecosystem will only grow over the coming months. 

<sup>7 Manipulating the history of dispatched actions and state changes to emulate a point in time of application interaction.</sup>