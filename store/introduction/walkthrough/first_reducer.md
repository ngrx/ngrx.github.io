# Setting Up The First Reducer

([Work Along](https://plnkr.co/edit/GLSSfru8D1kAEOGaWC6B) | [Completed Lesson](https://plnkr.co/edit/ceA9BhPE6LYLtiwEgDMC))

Reducers are the foundation to your store application. As the application *store*  maintains state, reducers are the workhorse behind the manipulation and output of new state representations as actions are dispatched. Each reducer should be focused on a specific section, or slice of state, similar to a table in a database.

Creating reducer's is quite simple once you get used to one common idiom, *never mutate previous state* and *always return a new representation of state when a relevant action is dispatched*. If you are new to store or the Redux pattern this takes some practice to feel natural. Instead of using mutative methods like `push`, or reassigning values to previously existing objects, you will instead lean on none mutating methods like `concat` and `Object.assign` to return new values. Still confused? Let's see what this looks like in practice with our `people` reducer.

The people reducer needs to handle five actions, *adding a person*, *removing a person*, *adding guests to a person*, *removing guests from a person*, and *toggling whether they are attending the event*. To do this, we will create a reducer function, accepting previous state and the currently dispatched action. We then need to implement a case statement that performs the correct state recalculation when a relevant action is dispatched.

###### People Reducer
```ts
const details = (state, action) => {
  switch(action.type){
    case ADD_GUEST:
      if(state.id === action.payload){
          return Object.assign({}, state, {guests: state.guests + 1});
      }
      return state;
    case REMOVE_GUEST:
      if(state.id === action.payload){
          return Object.assign({}, state, {guests: state.guests - 1});
        }
      return state;
      
    case TOGGLE_ATTENDING:
      if(state.id === action.payload){
          return Object.assign({}, state, {attending: !state.attending});
      }
      return state;
      
    default:
      return state;
  }
}
//remember to avoid mutation within reducers
export const people = (state = [], action) => {
  switch(action.type){
    case ADD_PERSON:
      return [
        ...state,
        Object.assign({}, {id: action.payload.id, name: action.payload.name, guests:0, attending: false})
      ];
      
    case REMOVE_PERSON:
      return state
        .filter(person => person.id !== action.payload);
     //to shorten our case statements, delegate detail updates to second private reducer   
    case ADD_GUEST:
      return state.map(person => details(person, action));
      
    case REMOVE_GUEST:
      return state.map(person => details(person, action));
    
    case TOGGLE_ATTENDING:
      return state.map(person => details(person, action));
     //always have default return of previous state when action is not relevant   
    default:
      return state;
  }
}
```

**Next: [Configuring Store Actions](store_actions.md)**