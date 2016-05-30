# Approach
In the [core concepts](core_concepts.md) section I mentioned _abiding by the store contract_ when developing your application. What exactly does this mean in the context of the typical Angular setup and workflow which you have grown accustomed? Let's take a look. 

If you are coming from an Angular 1 background you are familiar with <sup>1</sup> two-way data binding. The controller model binds to the view and vice versa. The problem with this approach presents itself as your view becomes more complex, requiring controllers and directives to manage and represent significant state changes over time. This can quickly turn into a nightmare both to reason about and debug as one change effects another, which effects another, and so on.

Store promotes the idea of <sup>2</sup> one-way data flow and explicitly dispatched actions. All state updates are handled above your components in store, delegated to reducers. The only way to initiate a state update in your application is through dispatched actions, corresponding to a particular reducer case. This not only makes reasoning about state changes in your application easier, as updates are centralized, it leaves a clear audit trail in case of error. 
###### <sup>1</sup> Two-Way Data Binding
![counter two-way](http://imgur.com/JUimvDf.png)
###### <sup>2</sup> One Way Data-Flow
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
