# Slicing State for Views

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
    directives: [PersonList, PersonInput, FilterSelect, PartyStats]
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
