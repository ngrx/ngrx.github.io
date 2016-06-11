# Testing Selectors

Testing your applications selectors is actually quite straightforward. Using [`Observable.of`](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#static-method-of) you can 
set up a sample state, apply the selector under test, and assert against the result.

### Sample Selectors

For the sample tests we are going to use the book selectors from the [@ngrx/example-app](https://github.com/ngrx/example-app).
The state of the books reducer is as follows:

###### Interface - BooksState
```ts
export interface BooksState {
  ids: string[];
  entities: { [id: string]: Book };
};
```

The selectors under test allow us to retrieve book entities, get a single book, and check whether a particular book exists.

###### Book Selectors
```ts
export function getBookEntities() {
  return (state$: Observable<BooksState>) => state$
    .select(s => s.entities);
};

export function getBook(id: string) {
  return (state$: Observable<BooksState>) => state$
    .select(s => s.entities[id]);
}

export function hasBook(id: string) {
  return (state$: Observable<BooksState>) => state$
    .select(s => s.ids.includes(id));
}
```

Using the technique described above, let's write some tests for the books selectors.

###### Selector Tests
```ts
import {describe, expect, it} from "@angular/core/testing";
import {Observable} from 'rxjs/Observable';
import books, * as bookSelectors from '../books';

describe('The component reducer', () => {
    //specs for reducer...
    const fakeBooks = {
        ids: ['ExampleOne', 'ExampleTwo'],
        entities: { 'ExampleOne': {}, 'ExampleTwo': {} }
    } 
    describe("book selectors", () => {
        it("should retrieve the book entities", () => {
            /*
                 1. Emit one value, the fake state required for our test
            */
            Observable.of(fakeBooks)
            /*
                 2. Apply the selector under test
            */
            .let(bookSelectors.getBookEntities())
            .subscribe(entities => {
                /*
                    3. Assert against the result
                */
                expect(entities).toEqual(fakeBooks.entities);
            });    
        });

        it("should retrieve a single book by id", () => {
            Observable.of(fakeBooks)
                .let(bookSelectors.getBook('ExampleOne'))
                .subscribe(book => {
                    expect(book).toEqual(fakeBooks.entities['ExampleOne']);
                });    
        });

        it("should return false when a book does not exist", () => {
            Observable.of(mockComponents)
                .let(selectors.hasBook('Intro to ngrx'))
                .subscribe(result => {
                    expect(result).toBe(false);
                });    
        });

        it("should return true when a book does exist", () => {
            Observable.of(mockComponents)
                .let(selectors.hasBook('ExampleOne'))
                .subscribe(result => {
                    expect(result).toBe(true);
                });    
        });
    })
});
```

The process for testing selectors is always the same, setup a sample state structure, apply the appropriate selector with `let`, subscribe and assert against the result.
You should now be on your way to getting your application selectors under test!

**More: [Selector Recipes](README.md)**