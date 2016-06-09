# @ngrx/notify
### Web Notifications Powered by RxJS for Angular 2
[![Join the chat at https://gitter.im/ngrx/notify](https://badges.gitter.im/ngrx/notify.svg)](https://gitter.im/ngrx/notify?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Easily create and handle desktop notifications in Angular 2

### Installation
Install @ngrx/notify from npm:
```bash
npm install @ngrx/notify --save
```

Setup the providers, optionally providing global notification options:
```ts
import { NOTIFY_PROVIDERS, NOTIFY_GLOBAL_OPTIONS } from '@ngrx/notify';

bootstrap(App, [
  NOTIFY_PROVIDERS,
  { provide: NOTIFY_GLOBAL_OPTIONS, multi: true, useValue: { /* global options here */ } }
])
```

## Usage
### Requesting Notification Permission
Before creating notifications, you must resolve the app's notification permission:

```ts
class AppComponent {
  constructor(notify: Notify) {
    notify.requestPermission().subscribe(permission => {
      if (permission) {
        // continue
      }
    });
  }
}
```

### Creating a Notification
To create a notification observable, call the `open()` method with a title and optional config. The notification will be opened when you subscribe to the observable and will close after you unsubscribe from it. The observable will emit the instance of the notification every time it is clicked on:

```ts
notify.open('Hello world!', options)
  // Automatically close the notification after 5 seconds
  .takeUntil(Observable.timer(5000))
  // Close the notification after it has been clicked once
  .take(1)
  .subscribe(notification => {

  });
```

See the [documentation on MDN](https://developer.mozilla.org/en-US/docs/Web/API/Notification/Notification) for available options.