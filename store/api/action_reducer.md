# ActionReducer
### signature: `ActionReducer<T>(state: T, action: Action): T`

A reducer function outputting appropriate slice of state, given the previous state and current action. 

#### Example - Basic Reducer
```ts
import {ActionReducer, Action} from '@ngrx/store';

export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

export const counter: ActionReducer<number> = (state : number = 0, action : Action) => {

	switch (action.type) {
		case INCREMENT:
			return state + 1;

		case DECREMENT:
			return state - 1;

		default:
			return state;
	}
}
```

#### Example - Reducer With Objects
```ts
import {ActionReducer, Action} from "@ngrx/store";
import {Todo} from "../common/interfaces";
import {ADD_TODO, REMOVE_TODO, TOGGLE_TODO} from "../common/actions";

export const todos : ActionReducer<Todo[]> = (state : Todo[] = [], action: Action) => {
  switch(action.type) {
      case ADD_TODO:
          return [
              ...state,
              action.payload
          ];
      
      case REMOVE_TODO:
          return state.filter(todo => todo.id !== action.payload);
            
      case TOGGLE_TODO:
          return state.map(todo => {
            if(todo.id !== action.payload){
               return todo;
            }
            return Object.assign({}, todo, {
                complete: !todo.complete
            });
          });
          
      default:
          return state;
  }
};
```

> :file_folder: [https://github.com/ngrx/store/blob/master/src/reducer.ts](https://github.com/ngrx/store/blob/master/src/reducer.ts)