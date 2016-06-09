# select
### signature: `select<T, R>(pathOrMapFn: any, ...paths: string[]): Observable<R>`

Select slice of state based on supplied value. If a function or single string is supplied, `select` will call `map`
and `distinctUntilChanged`. If multiple strings are supplied `select` will call `pluck` and `distinctUntilChanged`, each returning the correct state slice.

> :file_folder: [https://github.com/ngrx/core/blob/master/lib/operator/select.ts](https://github.com/ngrx/core/blob/master/lib/operator/select.ts)