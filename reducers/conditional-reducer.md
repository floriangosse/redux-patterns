# Conditional Reducer

A conditional reducer is a pattern to conditionally execute a reducer. A possible solution is to implement it directly in
the affected reducer:

```js
function reducer (state = initialState, action) {
    if (state.scheduledAt >= action.payload.scheduledAt) {
      return state;
    }

    switch (action.type) {
        // ...
    }
}
```

## Higher-order function

A more flexible solution is a higher-order function which takes the reducer that should be reset and the 
condition function as arguments. A possible implementation could look like this:

```js
function conditionalify (condition) {
    return (reducer) => {
        const initialState = reducer(undefined, { type: null });
    
        return (state = initialState, action) => {
            if (!condition(state, action)) {
                return state;
            }
            
            return reducer(state, action);
        }
    }
}
```

This higher-order function also takes care about providing the initial state of the reducer and can be used standalone or 
with functions like `compose`:

```js
const condition = (state, action) => state.scheduledAt < action.payload.scheduledAt;

// standalone
export default conditionalify(condition)(reducer);

// with compose
export default compose(
    // ...
    conditionalify(condition),
    // ...
)(reducer);
