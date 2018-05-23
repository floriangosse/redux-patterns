# Reset Reducer

A reset reducer is a command pattern to conditionaly reset a reducer's state. One solution is to implement it
directly in the affected reducer. Here is an example:

```js
function reducer (state = initialState, action) {
    switch (action.type) {
        case 'LOGOUT':
            return initialState;
            
        default:
            return state;
    }
}
```

## Higher-order function

A more flexible solution is a higher-order function which takes the reducer that should be reset and the 
condition function as arguments. A possible implementation could look like this:

```js
function resetify (condition) {
    return (reducer) => {
        const initialState = reducer(undefined, { type: null });
    
        return (state = initialState, action) => {
            if (condition(state, action)) {
                return initialState;
            }
            
            return reducer(state, action);
        }
    }
}
```

This higher-order function also takes care about returning the initial state of the reducer and can be used standalone or 
with functions like `compose`:

```js
const condition = (state, action) => action.type === 'LOGOUT';

// standalone
export default resetify(condition)(reducer);

// with compose
export default compose(
    // ...
    resetify(condition),
    // ...
)(reducer);
