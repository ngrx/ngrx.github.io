# Frequently Asked Questions

Commonly asked questions and issues regarding @ngrx/store setup and use.

### How does @ngrx/store compare to Redux?
Check out this [conversation](https://github.com/ngrx/store/issues/16) for a full rundown on the inspiration for store and how it compares to Redux.

### After I dispatch an action my view does not update, what's up?
The most common culprit for your view not updating after a dispatched action is an impure reducer. When using the `select` function
the `distinctUntilChanged` operator is applied after mapping to the approriate slice of state. If the reference for the selected
slice of state has not changed, ie. mutating previous state instead of returning a new reference, your view will not receive the update from store.

> :bulb: [More on `distinctUntilChanged`](https://gist.github.com/btroncone/d6cf141d6f2c00dc6b35#distinctuntilchanged)

### Why am I getting an error when trying to use an RxJS operator?
It is likely you have not yet imported the correct operator for use in your application. An example operator import is: `import 'rxjs/add/operator/map'`;

### How do I handle side-effects, such as API calls, within an @ngrx/store application?
[@ngrx/effects](https://github.com/ngrx/effects) is an @ngrx library dedicated to handling and easily testing side-effects within your store applications.

### More to come...

## Other issues?

No worries! We have an active gitter channel [![Join the chat at https://gitter.im/ngrx/store](https://badges.gitter.im/ngrx/store.svg)](https://gitter.im/ngrx/store?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) or feel free to [create an issue](https://github.com/ngrx/store/issues).  