# Basic Reducers



Reducers are the foundation of any Store or Redux-based application, describing sections of state and their potential transformations 
based on dispatched action types. It is the combination of your reducers that makes up a representation of application state 
at any given time.

Before we discuss how @ngrx/store reducers are structured and implemented, let's first look at the `reduce` function. 
Reduce takes an array of values, running a function against an accumulated value and current value, *reducing* your array 
into a single value upon completion. 
You can think of the accumulator as a snowball rolling downhill, gaining mass with each revolution. 
In the same way, the reduce accumulator is the result of applying your defined function to the current value through iteration. 

### Reduce
[*arr.reduce(callback[, initialValue])*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) | ([demo](http://jsbin.com/jetodulayi/edit?js,console))

```ts
const numberArray = [1,2,3];
/*
1.) accumulator: 1, current: 2
2.) accumulator: 3, current: 3
Final: 6
*/
const total = numberArray.reduce((accumulator, current) => accumulator + current);
console.log(total);

//Reduce with objects
const personInfo = [{name: 'Joe'}, {age: 31}, {birthday: '1/1/1985'}];
/*
1.) accumulator: {name: 'Joe'}, current: {age: 31}
2.) accumulator: {name: 'Joe', age: 31}, current: {birthday: '1/1/1985'}
Final: {name: 'Joe', age: 31, {birthday: '1'/1/1985'}}
*/
const fullPerson = personInfo.reduce((accumulator, current) => {
 return Object.assign({}, accumulator, current)
});
console.log(fullPerson);

//We can also provide an initial value for reduce as a second parameter
const personInfoStart = [{name: 'Joe'}, {age: 31}, {birthday: '1/1/1985'}];
/*
1.) accumulator: {favoriteLanguage: 'JavaScript'}, current: {name: 'Joe'}
2.) accumulator: {favoriteLanguage: 'JavaScript', name: 'Joe'}, current: {age: 31}
3.) accumulator: {favoriteLanguage: 'JavaScript', name: 'Joe', age: 31}, current: {birthday: '1/1/1985'}
Final: {favoriteLanguage: 'JavaScript', name: 'Joe', age: 31, birthday: '1/1/1985'}
*/
const fullPersonStart = personInfo.reduce((accumulator, current) => {
 return Object.assign({}, accumulator, current)
}, {favoriteLanguage: 'JavaScript'});
console.log(fullPersonStart);
``` 

### Reducing An Array of Actions

Reducer functions are used to manipulate specific slices of state. Reducers accept a state (accumulator) and action (current) parameter, 
exposing a switch statement (generally, although this could be handled multiple ways) defining action types in which the reducer is concerned.
Each time an action is dispatched, each reducer registered to store will be called (through the *root reducer*, created during `provideStore` at application bootstrap), 
passed the current state for that state slice (accumulator) and the dispatched action. 
If the reducer is registered to handle that action type, the appropriate state calculation will be performed and a new representation of 
state output. If not, the current state for that section will be returned. This is the core of Store and Redux state management.

Let's turn a basic reduce function into an @ngrx/store style `ActionReducer`.
Initially we are supplying an anonymous function which provides a sum of an array of numbers with reduce.

###### Sum with Reduce
```ts
//add values in an array
const example = [1,2,3].reduce((accumulator, current) => accumulator + current);

console.log(example); //6
```

Next, we can extract the anonymous reducer function out and name it `sum`, for the same result.

###### Extracting a Reduce Function
```ts
//named reducer function
const sum = (accumulator, current) => accumulator + current;
const example = [1,2,3].reduce(sum);

console.log(exampleTwo); //6
```

This works great, however in @ngrx/store we want state changes to be triggered by dispatched actions. Each action has a type and an optional payload. 
Let's rename the accumulator parameter to `state` and adjust the `current` parameter to accept an `action` type. 
Each action that passes through the 'reducer' function will return a new representation of state.

###### Refactoring to Accept Actions
```ts
const sumReducer = (state, action) => {
  switch(action.type){
    case 'ADD':
      return state + action.payload;
    default:
      return state;
  }
};
/*
  Each action has a type and an optional payload
*/
const actions = [{type: 'ADD', payload: 1},{type: 'ADD', payload: 2},{type: 'ADD', payload: 3}];
/*
  We can now reduce our array of actions into the same result as above.
*/
const exampleThree = actions.reduce(sumReducer, 0);

console.log(exampleThree); //6
```

To further solidify this concept, we can also create a counter reducer that increments or decrements a value based on an action type.

###### Counter Reducer
```ts
const counter = (state, action) => {
  switch(action.type){
      case 'INCREMENT':
        return state + 1;
      case 'DECREMENT':
        return state - 1;
      default:
        return state;
  }
};
//sample actions
const moreActions = [{type:'INCREMENT'}, {type:'INCREMENT'}, {type:'DECREMENT'}, {type:'INCREMENT'}];
const exampleFour = moreActions.reduce(counter, 0);

console.log(exampleFour); //2
```

If we wanted to use this reducer to manage the `counter` state in our @ngrx/store app it can be registered with the 
`provideStore` function at application bootstrap.

###### Registering Reducer With `provideStore`
```ts
import { bootstrap } from '@angular/platform-browser-dynamic';
import { provideStore } from '@ngrx/store';
import { App } from './myapp';

import { counterReducer } from './counter';

bootstrap(App, [
	provideStore({ counter: counterReducer })
]);
```

Now, as relevant actions are dispatched through the lifetime of our application
the counter state will be updated appropriately.

###### Dispatching Actions
```ts
import { Store } from '@ngrx/store';
import { INCREMENT, DECREMENT, RESET } from './counter';

interface AppState {
  counter: number;
}

@Component({
	selector: 'my-app',
	template: `
		<button (click)="increment()">Increment</button>
		<div>Current Count: {{ counter | async }}</div>
		<button (click)="decrement()">Decrement</button>
	`
})
class MyApp {
	counter: Observable<number>;

	constructor(public store: Store<AppState>){
		this.counter = store.select('counter');
	}

	increment(){
		this.store.dispatch({ type: INCREMENT });
	}

	decrement(){
		this.store.dispatch({ type: DECREMENT });
	}

	reset(){
		this.store.dispatch({ type: RESET });
	}
}
```

**Next: [Reducers With Objects](objects.md)**
