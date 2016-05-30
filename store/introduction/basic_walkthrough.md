# Basic Walkthrough
------

#### The Sample Application

The <sup>1</sup> sample application we will be building is a simple party planner. The user should be able to *enter a list of attendees and their guests*, *keep track of who has confirmed attendance*, *filter attendees by particular criteria*, and quickly *view important statistics regarding the event*. Throughout the creation of the application we will explore the core concepts of @ngrx/store and discuss popular patterns and best practices. 

There are two links provided above each section, *Work Along* and *Completed Lesson*. The *Work Along* link picks up at the beginning of each lesson if you wish to code along as the concepts are presented. Otherwise, the *Completed Lesson* link allows you to start at the end point of the current lesson. 

Without further ado, let's get started!
###### <sup>1</sup> Party Planner Application Layout
![party-planner layout](http://imgur.com/Cp59hVm.png)
#### Setting Up The First Reducer

([Work Along](https://plnkr.co/edit/GLSSfru8D1kAEOGaWC6B) | [Completed Lesson](https://plnkr.co/edit/ceA9BhPE6LYLtiwEgDMC))

Reducers are the foundation to your store application. As the application *store*  maintains state, reducers are the workhorse behind the manipulation and output of new state representations as actions are dispatched. Each reducer should be focused on a specific section, or slice of state, similar to a table in a database.

Creating reducer's is quite simple once you get used to one common idiom, *never mutate previous state* and *always return a new representation of state when a relevant action is dispatched*. If you are new to store or the Redux pattern this takes some practice to feel natural. Instead of using mutative methods like `push`, or reassigning values to previously existing objects, you will instead lean on none mutating methods like `concat` and `Object.assign` to return new values. Still confused? Let's see what this looks like in practice with our `people` reducer.

The people reducer needs to handle five actions, *adding a person*, *removing a person*, *adding guests to a person*, *removing guests from a person*, and *toggling whether they are attending the event*. To do this, we will create a reducer function, accepting previous state and the currently dispatched action. We then need to implement a case statement that performs the correct state recalculation when a relevant action is dispatched.

###### People Reducer
```ts
const details = (state, action) => {
  switch(action.type){
    case ADD_GUEST:
      if(state.id === action.payload){
          return Object.assign({}, state, {guests: state.guests + 1});
      }
      return state;
    case REMOVE_GUEST:
      if(state.id === action.payload){
          return Object.assign({}, state, {guests: state.guests - 1});
        }
      return state;
      
    case TOGGLE_ATTENDING:
      if(state.id === action.payload){
          return Object.assign({}, state, {attending: !state.attending});
      }
      return state;
      
    default:
      return state;
  }
}
//remember to avoid mutation within reducers
export const people = (state = [], action) => {
  switch(action.type){
    case ADD_PERSON:
      return [
        ...state,
        Object.assign({}, {id: action.payload.id, name: action.payload.name, guests:0, attending: false})
      ];
      
    case REMOVE_PERSON:
      return state
        .filter(person => person.id !== action.payload);
     //to shorten our case statements, delegate detail updates to second private reducer   
    case ADD_GUEST:
      return state.map(person => details(person, action));
      
    case REMOVE_GUEST:
      return state.map(person => details(person, action));
    
    case TOGGLE_ATTENDING:
      return state.map(person => details(person, action));
     //always have default return of previous state when action is not relevant   
    default:
      return state;
  }
}
```

#### Configuring Store Actions

([Work Along](https://plnkr.co/edit/ceA9BhPE6LYLtiwEgDMC) | [Completed Lesson](https://plnkr.co/edit/fRGN46atIlmjqoyV4G36))

The only way to modify state within a store application is by dispatching actions. Because of this, a log of actions should present a clear, readable, history of user interaction. Since actions are generally defined as string constants, aggregating these constants into a single file can also provide a one-stop dictionary, or reference for all relevant activity in your action. This can not only provide valuable insight into your applications intent, but also make it much easier to identify and recreate errors and bugs.

The creation of action constants is as easy as it sounds, simply export a string constant for each action within your application. These will then be used as the keys to your reducer case statements and the `type` for every dispatched action.

###### Initial Actions
```ts
/* 
  I prefer to keep all action constants in one file,
  this provides a one stop reference for all relevant user interaction
  within your application. It also allows those new to project to see
  everything your app 'does' in a quick glance.
*/

//Person Action Constants
export const ADD_PERSON = 'ADD_PERSON';
export const REMOVE_PERSON = 'REMOVE_PERSON';
export const ADD_GUEST = 'ADD_GUEST';
export const REMOVE_GUEST = 'REMOVE_GUEST';
export const TOGGLE_ATTENDING = 'TOGGLE_ATTENDING';
```

#### Utilizing Container Components

([Work Along](https://plnkr.co/edit/fRGN46atIlmjqoyV4G36) | [Completed Lesson](https://plnkr.co/edit/fiwsfPmb0mqnHCSTGdIY?p=preview))

Components in your Store application will fall into two categories, <sup>2</sup> **smart** and **dumb**. So what does it mean to be a smart component vs a dumb component? 

**Smart**, or **Container** components should be your root level, routable components. These components generally have direct access to store or a derivative. Smart components handle view events and the dispatching of actions, whether through a service or directly. Smart components also handle the logic behind events emitted up from child components within the same view.

**Dumb**, or **Child** components are generally for presentation only, relying exclusively on `@Input` parameters, acting on the receieved data in an appropriate manner. When relevant events occur in dumb components, they are emitted up to be handled by a parent smart component. Dumb components will make up the majority of your application, as they should be small, focused, and reusable.

The party planner application will need a single container component. This component will be responsible for passing appropriate state down to each child component and dispatching actions based on events emitted by our dumb components, `person-input`, `person-list`, and in the future `party-stats`. For now, we will manually subscribe to store in the constructor, setting updates to a component-level property. In future lessons we will explore how to utilize the `AsyncPipe` to reduce some of this boilerplate.


###### Container Component
```ts
@Component({
    selector: 'app',
    template: `
      <h3>@ngrx/store Party Planner</h3>
      <person-input
        (addPerson)="addPerson($event)"
      >
      </person-input>
      <person-list
        [people]="people"
        (addGuest)="addGuest($event)"
        (removeGuest)="removeGuest($event)"
        (removePerson)="removePerson($event)"
        (toggleAttending)="toggleAttending($event)"
      >
      </person-list>
    `,
    directives: [PersonList, PersonInput]
})
export class App {
    public people;
    private subscription;
    
    constructor(
     private _store: Store
    ){
      /* 
        demonstrating use without the async pipe,
        we will explore the async pipe in the next lesson
      */
      this.subscription = this._store
        .select('people')
        .subscribe(people => {
          this.people = people;
      });
    }
    //all state-changing actions get dispatched to and handled by reducers
    addPerson(name){
      this._store.dispatch({type: ADD_PERSON, payload: {id: id(), name})
    }
    
    addGuest(id){
      this._store.dispatch({type: ADD_GUEST, payload: id});
    }
    
    removeGuest(id){
      this._store.dispatch({type: REMOVE_GUEST, payload: id});
    }
    
    removePerson(id){
      this._store.dispatch({type: REMOVE_PERSON, payload: id});
    }
    
    toggleAttending(id){
      this._store.dispatch({type: TOGGLE_ATTENDING, payload: id})
    }
    /*
      if you do not use async pipe and create manual subscriptions
      always remember to unsubscribe in ngOnDestroy
    */
    ngOnDestroy(){
      this.subscription.unsubscribe();
    }
}
```

###### Dumb Component - PersonList
```ts
@Component({
    selector: 'person-list',
    template: `
      <ul>
        <li 
          *ngFor="#person of people"
          [class.attending]="person.attending"
        >
           {{person.name}} - Guests: {{person.guests}}
           <button (click)="addGuest.emit(person.id)">+</button>
           <button *ngIf="person.guests" (click)="removeGuest.emit(person.id)">-</button>
           Attending?
           <input type="checkbox" [(ngModel)]="person.attending" (change)="toggleAttending.emit(person.id)" />
           <button (click)="removePerson.emit(person.id)">Delete</button>
        </li>
      </ul>
    `
})
export class PersonList {
    /*
      "dumb" components do nothing but display data based on input and 
      emit relevant events back up for parent, or "container" components to handle
    */
    @Input() people;
    @Output() addGuest = new EventEmitter();
    @Output() removeGuest = new EventEmitter();
    @Output() removePerson = new EventEmitter();
    @Output() toggleAttending = new EventEmitter();
}
```

###### Dumb Component - PersonInput
```ts
@Component({
    selector: 'person-input',
    template: `
      <input #personName type="text" />
      <button (click)="add(personName)">Add Person</button>
    `
})
export class PersonInput {
    @Output() addPerson = new EventEmitter();

    add(personInput){
      this.addPerson.emit(personInput.value);
      personInput.value = '';
    }
}
```

###### <sup>2</sup> Smart (Container) and Dumb Components
![container components](http://imgur.com/6DKkS5x.png)

#### Utilizing the AsyncPipe

([Work Along](https://plnkr.co/edit/fiwsfPmb0mqnHCSTGdIY?p=preview) | [Completed Lesson](https://plnkr.co/edit/07HfvgkoNejiC8joyHNL?p=preview))

The `AsyncPipe` is a unique, stateful pipe in Angular 2 meant for handling both Observables and Promises. When using the `AsyncPipe` in a template expression with Observables, the supplied Observable is subscribed to, with emitted values being displayed within your view. This pipe also handles unsubscribing to the supplied observable, saving you the mental overhead of manually cleaning up subscriptions in `ngOnDestroy`. In a Store application, you will find yourself leaning on the `AsyncPipe` heavily in nearly all of your component views. For a more detailed explanation of exactly how the `AsyncPipe` works, check out my article [*Understand and Utilize the AsyncPipe in Angular 2*](http://briantroncone.com/?p=623).

Utilizing the `AsyncPipe` in our templates is easy. Once included in the `pipes` property of the `@Component` decorator
you can pipe any Observable (or promise) through `async` and a subscription will be created, updating the template value on source emission. Because we are using the `AsyncPipe`, we can also remove the manual subscription from the component constructor and `unsubscribe` from the `ngOnDestroy` lifecycle hook. This is now handled for us behind the scenes.

###### Refactoring to Async Pipe
```ts
@Component({
    selector: 'app',
    template: `
      <h3>@ngrx/store Party Planner</h3>
      <person-input
        (addPerson)="addPerson($event)"
      >
      </person-input>
      <person-list
        [people]="people | async"
        (addGuest)="addGuest($event)"
        (removeGuest)="removeGuest($event)"
        (removePerson)="removePerson($event)"
        (toggleAttending)="toggleAttending($event)"
      >
      </person-list>
    `,
    directives: [PersonList, PersonInput],
    pipes: [AsyncPipe]
})
export class App {
    public people;
    private subscription;
    
    constructor(
     private _store: Store
    ){
      /*
        Observable of people, utilzing the async pipe
        in our templates this will be subscribed to, with
        new values being dispayed in our template.
        Unsubscribe wil be called automatically when component
        is disposed.
      */
      this.people = _store.select('people');
    }
    //all state-changing actions get dispatched to and handled by reducers
    addPerson(name){
      this._store.dispatch({type: ADD_PERSON, payload: name})
    }
    
    addGuest(id){
      this._store.dispatch({type: ADD_GUEST, payload: id});
    }
    
    removeGuest(id){
      this._store.dispatch({type: REMOVE_GUEST, payload: id});
    }
    
    removePerson(id){
      this._store.dispatch({type: REMOVE_PERSON, payload: id});
    }
    
    toggleAttending(id){
      this._store.dispatch({type: TOGGLE_ATTENDING, payload: id})
    }
    //ngOnDestroy to unsubscribe is no longer necessary
}
```
#### Taking Advantage of ChangeDetection.OnPush

([Work Along](https://plnkr.co/edit/07HfvgkoNejiC8joyHNL?p=preview) | [Completed Lesson](https://plnkr.co/edit/BnaL3Mz1sLImeROMnE3z?p=preview))

Utilizing a centralized state tree in Angular 2 can not only bring benefits in predictability and maintability, but also performance. To enable this performance benefit we can utilize the `changeDetectionStrategy` of `OnPush`. 

The concept behind `OnPush` is straightforward, when components rely solely on inputs, and those input references do not change, Angular can skip running change detection on that section of the component tree. As discussed previously, all delegating of state should be handled in smart, or top level components. This leaves the majority of components in our application relying solely on input, safely allowing us to set the `ChangeDetectionStrategy` to `OnPush` in the component definition. These components can now forgo change detection until necessary, giving us a free performance boost.

To utilize `OnPush` change detection in our components, we need to set the `changeDetection` propery in the `@Component` decorator to `ChangeDetection.OnPush`. That's it! Angular will now ignore change detection on our *dumb* components and children of these components until there is a change in their input references. 

###### Updating to ChangeDetection.OnPush
```ts
@Component({
    selector: 'person-list',
    template: `
      <ul>
        <li 
          *ngFor="#person of people"
          [class.attending]="person.attending"
        >
           {{person.name}} - Guests: {{person.guests}}
           <button (click)="addGuest.emit(person.id)">+</button>
           <button *ngIf="person.guests" (click)="removeGuest.emit(person.id)">-</button>
           Attending?
           <input type="checkbox" [(ngModel)]="person.attending" (change)="toggleAttending.emit(person.id)" />
           <button (click)="removePerson.emit(person.id)">Delete</button>
        </li>
      </ul>
    `,
    changeDetection: ChangeDetectionStrategy.OnPush
})
/*
  with 'onpush' change detection, components which rely solely on 
  input can skip change detection until those input references change,
  this can supply a significant performance boost
*/
export class PersonList {
    /*
      "dumb" components do nothing but display data based on input and 
      emit relevant events back up for parent, or "container" components to handle
    */
    @Input() people;
    @Output() addGuest = new EventEmitter();
    @Output() removeGuest = new EventEmitter();
    @Output() removePerson = new EventEmitter();
    @Output() toggleAttending = new EventEmitter();
}
```
#### Expanding State

([Work Along](https://plnkr.co/edit/BnaL3Mz1sLImeROMnE3z?p=preview) | [Completed Lesson](https://plnkr.co/edit/JS4GAzZhfY0Bf1df8Ys0?p=preview))

The majority of store applications will be made up of multiple reducers, each managing their own slice of state. For this example we will have two, one to manage party attendees and the other to represent the currently active filter being applied to this list. Let's first write our action constants, specifiying the allowed filters to be applied by the user.

It's now time to create the *partyFilter* reducer. For this functionality we have a couple of options. The first would be to simply return a string representation of which filter should be applied. We could then write a method, either in a service or component, that filters the list based on the current active party filter. While this works, it is more extensible to return the function to be applied to the party list based on the current party filter state. In the future, adding more filters is as simple as creating a new case statement to return the appropriate projection function.

###### Party Filter Reducer
```ts
import {
  SHOW_ATTENDING,
  SHOW_ALL,
  SHOW_WITH_GUESTS
} from './actions';

//return appropriate function depending on selected filter
export const partyFilter = (state = person => person, action) => {
    switch(action.type){
        case SHOW_ATTENDING:
            return person => person.attending;
        case SHOW_ALL:
            return person => person;
        case SHOW_WITH_GUESTS:
            return person => person.guests;
        default:
            return state;
    }
};
```

###### Party Filter Actions
```ts
//Party Filter Constants
export const SHOW_ATTENDING = 'SHOW_ATTENDING';
export const SHOW_ALL = 'SHOW_ALL';
export const SHOW_WITH_GUESTS = 'SHOW_GUESTS';
```

###### Party Filter Select
```ts
import {Component, Output, EventEmitter} from "angular2/core";
import {
  SHOW_ATTENDING,
  SHOW_ALL,
  SHOW_WITH_GUESTS
} from './actions';

@Component({
    selector: 'filter-select',
    template: `
      <div class="margin-bottom-10">
        <select #selectList (change)="updateFilter.emit(selectList.value)">
            <option *ngFor="#filter of filters" value="{{filter.action}}">
                {{filter.friendly}}
            </option>
        </select>
      </div>
    `
})
export class FilterSelect {
    public filters = [
        {friendly: "All", action: SHOW_ALL}, 
        {friendly: "Attending", action: SHOW_ATTENDING}, 
        {friendly: "Attending w/ Guests", action: SHOW_WITH_GUESTS}
      ];
    @Output() updateFilter : EventEmitter<string> = new EventEmitter<string>();
}
```
#### Slicing State for Views

([Work Along](https://plnkr.co/edit/JS4GAzZhfY0Bf1df8Ys0?p=preview) | [Completed Lesson](https://plnkr.co/edit/ep1LEi0Xc8y1I3Zvulnr?p=preview))


Store can be thought of as your *client-side database*. Because store is the aggregate of state in our application we need to be able to *query* against it, returning relevant state slices and projections. This is an area where the RxJS foundation of store really shines.

To select appropriate slices of state for consumption you can start by using the Rx implementations of classic JavaScript collection operations in which you have grown accustom. Store also provides a helper function, `select`, which accepts a string or function, applying `map` and `distinctUntilChanged` behind the scenes to return an `Observable` of the appropriate section of state. As your needs progress, RxJS offers a multitude of powerful operators to suit any use-case.

In this example, we are going to slice our state into multiple `Observables`, representing the party attendees, current filter, attendance and guest count. In the next lesson we will see how to streamline this utilizing popular RxJS operators.

###### Projecting State Without Combination Operators
```ts
@Component({
    selector: 'app',
    template: `
      <h3>@ngrx/store Party Planner</h3>
      <party-stats
        [invited]="(people | async)?.length"
        [attending]="(attending | async)?.length"
        [guests]="(guests | async)"
      >
      </party-stats>
      <filter-select
        (updateFilter)="updateFilter($event)"
      >
      </filter-select>
      <person-input
        (addPerson)="addPerson($event)"
      >
      </person-input>
      <person-list
        [people]="people | async"
        [filter]="filter | async"
        (addGuest)="addGuest($event)"
        (removeGuest)="removeGuest($event)"
        (removePerson)="removePerson($event)"
        (toggleAttending)="toggleAttending($event)"
      >
      </person-list>
    `,
    directives: [PersonList, PersonInput, FilterSelect, PartyStats],
    pipes: [AsyncPipe]
})
export class App {
    public people;
    private subscription;
    
    constructor(
     private _store: Store
    ){
      this.people = _store.select('people');
      /*
        this is a naive way to handle projecting state, we will discover a better
        Rx based solution in next lesson
      */
      this.filter = _store.select('partyFilter');
      this.attending = this.people.map(p => p.filter(person => person.attending));
      this.guests = this.people
          .map(p => p.map(person => person.guests)
                     .reduce((acc, curr) => acc + curr, 0));
    }
    //...rest of component

}
```

Now that we have all the necessary data, we can pass it down to our "dumb" components to be presented accordingly. Our container component will handle any actions emitted back up the chain, dispatching the appropriate events.

###### Manually Passing Down and Applying Filters
```ts
@Component({
    selector: 'person-list',
    template: `
      <ul>
        <li 
          *ngFor="#person of people.filter(filter)"
          [class.attending]="person.attending"
        >
           {{person.name}} - Guests: {{person.guests}}
           <button (click)="addGuest.emit(person.id)">+</button>
           <button *ngIf="person.guests" (click)="removeGuest.emit(person.id)">-</button>
           Attending?
           <input type="checkbox" [checked]="person.attending" (change)="toggleAttending.emit(person.id)" />
           <button (click)="removePerson.emit(person.id)">Delete</button>
        </li>
      </ul>
    `,
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class PersonList {
    @Input() people;
    //for now, we will pass filter down and apply
    @Input() filter;
    @Output() addGuest = new EventEmitter();
    @Output() removeGuest = new EventEmitter();
    @Output() removePerson = new EventEmitter();
    @Output() toggleAttending = new EventEmitter();
}

```

#### Projecting State for View with combineLatest and withLatestFrom

([Work Along](https://plnkr.co/edit/ep1LEi0Xc8y1I3Zvulnr?p=preview) | [Completed Lesson](https://plnkr.co/edit/enZ0uJDYfDOgQNY2jgiw?p=preview))

While building out your @ngrx/store application, many of your components will require slices of state output from multiple reducers. One example of this would be a list that is adjusted by a filter, both managed through store. When the filter is changed, the list needs to be updated to reflect this change. So how do we facilitate this interaction? RxJS provides two operators that you will consistently utilize, `combineLatest` and `withLatestFrom`. 

The `combineLatest` operator accepts an unspecified number of observables, emitting the last emitted value from each when any of the provided observables emit. These values are passed to a projection function for you to form the appropriate projection.

######combineLatest Example

([demo](http://jsbin.com/lumaqanoha/1/edit?js,console))

```js
//timerOne emits first value at 1s, then once every 4s
const timerOne = Rx.Observable.timer(1000, 4000);
//timerTwo emits first value at 2s, then once every 4s
const timerTwo = Rx.Observable.timer(2000, 4000)
//timerThree emits first value at 3s, then once every 4s
const timerThree = Rx.Observable.timer(3000, 4000)

//when one timer emits, emit the latest values from each timer as an array
const combined = Rx.Observable
.combineLatest(
    timerOne,
    timerTwo,
    timerThree
);

const subscribe = combined.subscribe(latestValues => {
	//grab latest emitted values for timers one, two, and three
	const [timerValOne, timerValTwo, timerValThree] = latestValues;
  /*
  	Example:
    timerOne first tick: 'Timer One Latest: 1, Timer Two Latest:0, Timer Three Latest: 0
    timerTwo first tick: 'Timer One Latest: 1, Timer Two Latest:1, Timer Three Latest: 0
    timerThree first tick: 'Timer One Latest: 1, Timer Two Latest:1, Timer Three Latest: 1
  */
  console.log(
    `Timer One Latest: ${timerValOne}, 
     Timer Two Latest: ${timerValTwo}, 
     Timer Three Latest: ${timerValThree}`
   );
});

//combineLatest also takes an optional projection function
const combinedProject = Rx.Observable
.combineLatest(
    timerOne,
    timerTwo,
    timerThree,
    (one, two, three) => {
      return `Timer One (Proj) Latest: ${one}, 
              Timer Two (Proj) Latest: ${two}, 
              Timer Three (Proj) Latest: ${three}`
    }
);
//log values
const subscribe = combinedProject.subscribe(latestValuesProject => console.log(latestValuesProject));
```


The `withLatestFrom` operator is similar, except it only combines the last emitted values from the provided observables when the source observable emits. This is useful when your projection depends first on a single source, aided by multiple other sources.

######withLatestFrom Example

([demo](http://jsbin.com/qicuzovepo/edit?js,console))

```ts

//Create an observable that emits a value every second
const myInterval = Rx.Observable.interval(1000);
//Create an observable that emits immediately, then every 5 seconds
const myTimer = Rx.Observable.timer(0, 5000);
//Every time interval emits, also get latest from timer and add the two values
const latest = myInterval
  .withLatestFrom(myTimer)
  .map(([interval, timer]) => {
    console.log(`Latest Interval: ${interval}`);
    console.log(`Latest Timer: ${timer}`);
    return interval + timer;
  });
//log total
const subscribe = latest.subscribe(val => console.log(`Total: ${val}`));
```
Now that we have an understanding of these *combination* operators, we can clean up our previous example by using `Observable.combineLatest`. Instead of creating a new observable for each statistic, the state from *people* and *partyFilter* can be combined any time either updates, executing our projection function to calculate party statistics and properly filter the people list.

###### Refactoring Container View with combineLatest
```ts
@Component({
    selector: 'app',
    template: `
      <h3>@ngrx/store Party Planner</h3>
      <party-stats
        [invited]="(model | async)?.total"
        [attending]="(model | async)?.attending"
        [guests]="(model | async)?.guests"
      >
      {{guests | async | json}}
      </party-stats>
      <filter-select
        (updateFilter)="updateFilter($event)"
      >
      </filter-select>
      <person-input
        (addPerson)="addPerson($event)"
      >
      </person-input>
      <person-list
        [people]="(model | async)?.people"
        (addGuest)="addGuest($event)"
        (removeGuest)="removeGuest($event)"
        (removePerson)="removePerson($event)"
        (toggleAttending)="toggleAttending($event)"
      >
      </person-list>
    `,
    directives: [PersonList, PersonInput, FilterSelect, PartyStats],
    pipes: [AsyncPipe]
})
export class App {
    public model;
    
    constructor(
     private _store: Store
    ){
      /*
        Every time people or partyFilter emits, pass the latest
        value from each into supplied function. We can then calculate
        and output statistics.
      */
      this.model = Observable.combineLatest(
          _store.select('people')
          _store.select('partyFilter'),
          (people, filter) => {
          return {
            total: people.length
            people: people.filter(filter),
            attending: people.filter(person => person.attending).length,
            guests: people.reduce((acc, curr) => acc + curr.guests, 0)
          }
        });
    }
    //...rest of component
}
```
#### Extracting Selectors for Reuse

([Work Along](https://plnkr.co/edit/ep1LEi0Xc8y1I3Zvulnr?p=preview) | [Completed Lesson](https://plnkr.co/edit/LB3mId2knRUQTLlx4p9G?p=preview))

Through the course of building your application you will often utilize similar queries, or projections of state in your views. A common way to eliminate the duplication of this logic is to place popular selections into services, injecting these services where needed in your components or other services. While this certainly works, there is another more flexible, composable way to tackle this issue.

Because nothing about these projections is Angular specific, we can export each small query, or <sup>3</sup> `selector` independantly, without the need for Angular service wrapping. Leveraging the `let` operator, these selectors can then be mixed and matched for the desired result, whether in components, services, or middleware. This toolbox of targeted, composable queries is called the **selector pattern**.

To accomplish this we will create a new file to house our application selectors. We can then extract the projection function being applied in `combineLatest` to filter people and produce statistics into a *selector*.

###### Party Model Selector
```ts
export const partyModel = () => {
  return state => state
    .map(([people, filter]) => {
      return {
            total: people.length
            people: people.filter(filter),
            attending: people.filter(person => person.attending).length,
            guests: people.reduce((acc, curr) => acc + curr.guests, 0)
          }
    })
};
```

For futher demonstration, let's create two more selectors, one to return an obervable of party attendees and another, building on the previous selector, to calculate the percent of people attending based on those invited. This shows how easy it is to compose these small, focused selectors into powerful queries for use in views and middleware.

###### Percent Attendance Selector
```ts
export const attendees = () => {
  return state => state
    .map(s => s.people)
    .distinctUntilChanged();
};

export const percentAttending = () => {
  return state => state
    //build on previous selectors
    .let(attendees())
    .map(p => {
      const totalAttending = p.filter(person => person.attending).length;
      const total = p.length;
      return total > 0 ? (totalAttending / total) * 100 : 0;
    });
};
```

Applying selectors is easy, simply apply the `let` operator to the appropriate Observable, supplying the selector(s) of your choosing.

######Applying Selectors In Container Component
```ts
export class App {
    public model;
    
    constructor(
     private _store: Store
    ){
      /*
        Every time people or partyFilter emits, pass the latest
        value from each into supplied function. We can then calculate
        and output statistics.
      */
      this.model = Observable.combineLatest(
            _store.select('people')
            _store.select('partyFilter')
          )
          //extracting party model to selector
          .let(partyModel());
      //for demonstration on combining selectors
      this.percentAttendance = _store.let(percentAttending());
    }
    //...rest of component
}
```

###### <sup>3</sup> Selector Interface
```ts
interface Selector<T,V> {
  (state: Observable<T>): Observable<V>
}
```

#### Introducing Store Middleware

([Work Along](https://plnkr.co/edit/LB3mId2knRUQTLlx4p9G?p=preview) | [Completed Lesson](https://plnkr.co/edit/tRAEE5XJcljrFnQbVg0p?p=preview))

*Note: Middleware has been remove in ngrx/store v2. Please see the [meta-reducers](#implementing-a-meta-reducer) section for how to create similar functionality.*

Middleware provides an easy-to-utilize entry point for inserting custom functionality into the action pipeline. Store middleware comes in two flavors, `preMiddleware` and `postMiddleware`, implemented using the RxJS `let` operator. Pre-middleware is applied before dispatched actions hit your reducers, passed an `Observable<Action>`. Post-middleware is invoked before the new representation of state is `next`ed into store, passed an `Observable<State>`

The uses for middleware are many, from handling side-effect with sagas, to advanced logging of actions, to automatically syncing slices of state to local storage. In the Redux community, an entire ecosystem of custom middleware has been established. For our use, we'll be implementing simple logger middleware to keep track of dispatched actions and subsequent updates to state.

Because middleware is passed the entire observable, either of `Action` or `State`, we simply need to provide functions that receieve the source observable, returning an observable. This allows us to utilize the wide-array of operators RxJS exposes to obtain the desired functionality. One such operator, `do`, provides a transparent way to perform aribtrary actions as values flow through the dispatcher pipeline. 

Let's implement basic logging middleware, making use of the `do` operator in both pre and post middleware to log dispatched actions and new representations of state. 

###### Basic Logger Middleware
```ts
//pre middleware takes an observable of actions, returning an observable
export const actionLogger = action => {
  return action.do(a => console.log('DISPATCHED ACTION:', a));
}
//post middleware takes an observable of state, returning observable
export const stateLogger = state => {
  return state.do(s => console.log('NEW STATE:', s));
}
```

###### Providing Middleware on Bootstrap
```ts
bootstrap(App, [
  provideStore({people, partyFilter}),
  usePreMiddleware(actionLogger),
  usePostMiddleware(stateLogger)
]);
```

#### Middleware with Dependencies

([Work Along](https://plnkr.co/edit/tRAEE5XJcljrFnQbVg0p?p=preview) | [Completed Lesson](https://plnkr.co/edit/oBQ1rLfMV8MiPIzxW8t7?p=preview))

*Note: Middleware has been remove in ngrx/store v2. Please see the [meta-reducers](#implementing-a-meta-reducer) section for how to create similar functionality.*

When creating custom middleware you may find the need to utilize your Angular services. Store comes with a helper function, <sup>4</sup> `createMiddleware`, to make this process easy. The `createMiddleware` function takes a factory function, accepting any number of dependecies supplied as a second paramater. Behind the scenes, Store handles the Angular 2 provider setup, creating the proper tokens and injecting the declared Angular depencies into your middleware factory function. You can then utilize these dependencies as you wish to produce the desired result.

In this example we are going to create a `LocalStorageService`, wrapping the local storage API for use in the party planner application. This service will then be injected into our custom `localStorageMiddleware`, which accepts a state key to keep in sync with local storage. All that is left to do is apply this as `postMiddleware` on application bootstrap and any state updates to the predefined section will be reflected in local storage.

###### Sample Middleware Service
```ts
import {Injectable} from 'angular2/core';
//simple service wrapping local storage
@Injectable()
export class LocalStorageService {
    setItem(key, value){
      localStorage.setItem(key, JSON.stringify(value));
    }
    
    getItem(key){
      return JSON.parse(localStorage.getItem(key));
    }
}
```

###### Create Middleware With Dependencies Using createMiddleware
```ts
/*
  create middleware with a dependency on the localStorageService
  basic example, accept state key to sync with local storage
*/
export const localStorageMiddleware = key => createMiddleware(localStorageService => {
  return state => {
    //sync specified state slice with local storage
    return state.do(state => localStorageService.setItem(key, state[key]));
  }
}, [LocalStorageService]);
```
<sup>4 `createMiddleware(useFactory: (...deps: any[]) => Middleware, deps?: any[]): Provider`</sup>

#### Rehydrating Application State on Bootstrap

([Work Along](https://plnkr.co/edit/oBQ1rLfMV8MiPIzxW8t7?p=preview) | [Completed Lesson](https://plnkr.co/edit/4wFpmsCpDhGW6h1WopyC?p=preview))

*Note: Middleware has been remove in ngrx/store v2. Please see the [meta-reducers](#implementing-a-meta-reducer) section for how to create similar functionality.*

There are times when you will want to provide initial state to your reducers, outside of initial state supplied as a default function parameter. In these scenarios, store provides an `INITIAL_STATE` token which can be imported an overridden in order update state accordingly on application bootstrap. Behind the scenes, store will first check if the data has been initialized under the `INITIAL_STATE` token and if so, use this as the default initial state for the appropriate reducer.

To demonstrate this, we will expand upon the previous example and rehydrate the state collected in local store when the application is close or the page is refreshed. To handle this, we expose a function that accepts a key to rehydrate, returning a Angular provider for `INITIAL_STATE`, returning our data from local storage as the value. Now, when store asks for `INITIAL_STATE`, Angular will return our rehydrated data.


###### Rehydrate Helper Function
```ts
import {provide, Provider} from 'angular2/core';
import {INITIAL_STATE} from '@ngrx/store';

export const rehydrateState = key => {
  //override initial state token for use in store
  return provide(INITIAL_STATE, { 
    useValue: { [key]: JSON.parse(localStorage.getItem(key))}; 
  });
};
```

###### Rehydrate On Bootstrap
```ts
bootstrap(App, [
  LocalStorageService,
  provideStore({people, partyFilter}),
  usePreMiddleware(actionLogger),
  usePostMiddleware(stateLogger, localStorageMiddleware('people')),
  rehydrateState('people')
]);
```

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
  LocalStorageService,
  //wrap people in reset meta-reducer
  provideStore({people: reset(people), partyFilter}),
  usePreMiddleware(actionLogger),
  usePostMiddleware(stateLogger, localStorageMiddleware('people'))
]);
```

It is worth noting that the store *root reducer* is itself a meta-reducer, created behind the scenes when calling `provideStore` (by <sup>14</sup> `combineReducers`). For each dispatched action, the root reducer invokes each application reducer with the previous state and current action, returning an object map of `[reducer]: state[reducer]`. 

###### <sup>5</sup> combineReducers
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