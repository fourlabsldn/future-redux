# Future Redux
Redux is a predictable state container for JavaScript apps.  

This is a fork of [Redux](http://redux.js.org) with a single difference: **The return value of reducers**.

In Redux your reducer returns a state object. This is very straight forward, but makes dealing with asynchronous updates quite tricky (there are [more than 60 different libraries](https://github.com/markerikson/redux-ecosystem-links/blob/master/side-effects.md) tackling this problem).

In Future Redux your update function returns an array containing at most two items:
  - The first is your state object
  - The second one is optional and is a `[Future](https://github.com/ramda/ramda-fantasy/blob/master/docs/Future.md)` object, which is similar to a Promise.

The resolve function of the future is equivalent to store.dispatch. You can use it like this:

``` javascript
// This is the future implementation we will use.
// You can use any implementation with a `fork` method.
import { Future } from "ramda-fantasy";

function myReducer(state, action) {
  switch(action.type) {
    case "FETCH_CONTENT";
      return [
        state,
        new Future((reject, resolve) => {
          fetch("www.content.com/endpoint")
            .then(r => r.json())
            .then(content =>
              resolve({ type : "CONTENT_ARRIVED", value: content})
            )
        })
      ];

    case "CONTENT_ARRIVED":
      return [action.value]
  }
}
```

That's it. This way you can have your reducers return the description of a side effect without losing its purity and without having to hook up other libraries for that.

### License

MIT
