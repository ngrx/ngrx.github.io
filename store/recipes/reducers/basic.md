# Basic Reducers

Reducers are the foundation of any @ngrx/store application. Each reducer manages a slice, or section of state, and can be thought
of almost like a table in a database (store). Let's start with the basics to fully understand what a reducer is and the role they serve.
First, let's familiarize ourselves with the reduce function in JavaScript.

### Reduce
*arr.reduce(callback[, initialValue])*

The reduce function accepts an `accumulator` and the `current` value in the array, returning a new value which becomes the `accumulator` on the next iteration. 
A starting value can also be supplied as the second parameter.

```ts
//add values in an array
const example = [1,2,3].reduce((accumulator, current) => accumulator + current);

console.log(example); //6
```

Let's extract our anonymous reducer function out and name it `sum`, for the same result.

```ts
//named reducer function
const sum = (accumulator, current) => accumulator + current;
const example = [1,2,3].reduce(sum);

console.log(exampleTwo); //6
```

In @ngrx/store we want state changes to be caused by dispatched actions. Each action has a type and an optional payload. 
Let's rename the accumulator parameter to `state` and adjust the `current` parameter to accept an `action` type. Each action that passes through the 'reducer' function will return a new representation of state.

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

```ts
const counterReducer = (state, action) => {
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
const exampleFour = moreActions.reduce(counterReducer, 0);

console.log(exampleFour); //2
```

*Discuss scan...in progress*
