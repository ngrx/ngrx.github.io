# Utilizing Container Components

([Work Along](https://plnkr.co/edit/fRGN46atIlmjqoyV4G36) | [Completed Lesson](https://plnkr.co/edit/fiwsfPmb0mqnHCSTGdIY?p=preview))

Components in your Store application will fall into two categories, <sup>1</sup> **smart** and **dumb**. So what does it mean to be a smart component vs a dumb component? 

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

###### <sup>1</sup> Smart (Container) and Dumb Components
![container components](http://imgur.com/LbhNOFX.png)