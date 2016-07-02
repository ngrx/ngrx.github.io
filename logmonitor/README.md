# @ngrx/store-log-monitor

[![Join the chat at https://gitter.im/ngrx/store](https://badges.gitter.im/ngrx/store.svg)](https://gitter.im/ngrx/store?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)


Port of [redux-devtools-log-monitor](https://github.com/gaearon/redux-devtools-log-monitor) for Angular 2 and [@ngrx/store-devtools](https://github.com/ngrx/store-devtools)


### Setup

*Install @ngrx/store-log-monitor from npm*
```bash
npm install @ngrx/store-log-monitor --save
```

*Configure the monitor when instrumenting store*
```ts
import { instrumentStore } from '@ngrx/store-devtools';
import { useLogMonitor } from '@ngrx/store-log-monitor';

bootstrap(App, [
	instrumentStore({
		monitor: useLogMonitor({
			visible: true,
			position: 'right'
		})
	})
]);
```

*Add the StoreLogMonitorComponent to your app*

```ts
import { StoreLogMonitorComponent } from '@ngrx/store-log-monitor';

@Component({
	selector: 'app',
	directives: [ StoreLogMonitorComponent ],
	template: `
		<store-log-monitor toggleCommand="ctrl-h" positionCommand="ctrl-m"></store-log-monitor>
	`
})
export class App { }
```