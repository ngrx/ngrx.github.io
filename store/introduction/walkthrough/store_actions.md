# Configuring Store Actions

([Work Along](https://plnkr.co/edit/ceA9BhPE6LYLtiwEgDMC) | [Completed Lesson](https://plnkr.co/edit/fRGN46atIlmjqoyV4G36))

The only way to modify state within a store application is by dispatching actions. Because of this, a log of actions should present a clear, readable, history of user interaction. Actions are generally defined as string constants or as static string values on services encapsulating a particular action type. In the latter case, functions are provided to return an appropriate action given the correct input. These methods, which help standardize your actions while providing additional type safety, are known as *action creators*. 

For the case of our starter application we will export a string constant for each application action. These will then be used as the keys to our reducer case statements and the `type` for every dispatched action.

###### Initial Actions
```ts
//Person Action Constants
export const ADD_PERSON = 'ADD_PERSON';
export const REMOVE_PERSON = 'REMOVE_PERSON';
export const ADD_GUEST = 'ADD_GUEST';
export const REMOVE_GUEST = 'REMOVE_GUEST';
export const TOGGLE_ATTENDING = 'TOGGLE_ATTENDING';
```