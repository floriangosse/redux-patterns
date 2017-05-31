# Entities

An entities reducer handles and stores all existing entities at a single place. This makes it possible to easily store all 
entities e.g. in the local stoage for offline usage.

In other parts of your application you can access the entities by selectors.

## Structure
```json5
{
    "entities": {
        "users": {
            "123": {
                "id": 123,
                "name": "John Doe"
            }
        },
        "articles": {
            "10": {
                "title": "My First Article",
                "author": 123 // This is an user id
            }
        }
    }
}
```

## Example code

`entities.js`:
```js
// Here we store all our users
const users = (state = {}, action) => {
    swtich (action.type)
        case ADD_USER:
        case UPDATE_USER:
            const user = action.payload;
            return {
                ...state,
                [user.id]: user
            }
        
        default:
            return state;
};

// ... and here our articles
const articles = (state = {}, action) => {
    swtich (action.type)
        case ADD_ARTICLE:
        case UPDATE_ARTICLE:
            const article = action.payload;
            return {
                ...state,
                [article.id]: article
            }
        
        default:
            return state;
};

export default combineReducers({
    users: users,
    articles: articles
});
```

`store.js`:
```js
import entitiesReducer from './entities';

const rootReducer = combineReducers({
    entities: entitiesReducer
});

const store = createStore(rootReducer, initialState);
```
