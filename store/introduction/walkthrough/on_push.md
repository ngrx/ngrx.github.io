# Taking Advantage of ChangeDetection.OnPush

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
          *ngFor="let person of people"
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