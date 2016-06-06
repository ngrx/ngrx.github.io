# Expanding State

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
            <option *ngFor="let filter of filters" value="{{filter.action}}">
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