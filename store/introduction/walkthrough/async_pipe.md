# Utilizing the AsyncPipe

([Work Along](https://plnkr.co/edit/fiwsfPmb0mqnHCSTGdIY?p=preview) | [Completed Lesson](https://plnkr.co/edit/07HfvgkoNejiC8joyHNL?p=preview))

The `AsyncPipe` is a unique, stateful pipe in Angular 2 meant for handling both Observables and Promises. When using the `AsyncPipe` in a template expression with Observables, the supplied Observable is subscribed to, with emitted values being displayed within your view. This pipe also handles unsubscribing to the supplied observable, saving you the mental overhead of manually cleaning up subscriptions in `ngOnDestroy`. In a Store application, you will find yourself leaning on the `AsyncPipe` heavily in nearly all of your component views.

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
    directives: [PersonList, PersonInput]
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