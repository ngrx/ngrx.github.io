# Extracting Selectors for Reuse

([Work Along](https://plnkr.co/edit/ep1LEi0Xc8y1I3Zvulnr?p=preview) | [Completed Lesson](https://plnkr.co/edit/LB3mId2knRUQTLlx4p9G?p=preview))

Through the course of building your application you will often utilize similar queries, or projections of state in your views. A common way to eliminate the duplication of this logic is to place popular selections into services, injecting these services where needed in your components or other services. While this certainly works, there is another more flexible, composable way to tackle this issue.

Because nothing about these projections is Angular specific, we can export each small query, or <sup>1</sup> `selector` independantly, without the need for Angular service wrapping. Leveraging the `let` operator, these selectors can then be mixed and matched for the desired result, whether in components, services, or middleware. This toolbox of targeted, composable queries is called the **selector pattern**.

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

###### <sup>1</sup> Selector Interface
```ts
interface Selector<T,V> {
  (state: Observable<T>): Observable<V>
}
```