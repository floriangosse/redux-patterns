# Dynamic sub reducer

A dynamic subreducer will be used if you want to store a state based on a dynamic value like an article you fetch from an api.

## Example structure

```json5
{
    "articles": {
        // The id of an article
        "10": {
            "fetchStatus": "loading"
            "lastFetched": null
        }
    }
}
```

## Example code

`articles.js`:
```js
const initialArticleState = {
    fetchStatus: 'none',
    lastFetched: null
};

// This is our subreducer
const article = (state = initialArticleState, action) {
    switch (action.type) {
        case: FETCH_ARTICLE.REQUEST:
            return {
                ...state,
                fetchStatus: 'loading'
            };
        
        case: FETCH_ARTICLE.SUCCESS:
            return {
                ...state,
                fetchStatus: 'loaded',
                lastFetched: action.receivedAt
            };
        
        case: FETCH_ARTICLE.FAILURE:
            return {
                ...state,
                fetchStatus: 'failed',
                lastFetched: action.receivedAt
            };
    
        default:
            return state;
    }
};

const articles = (state = {}, action) => {
    const payload = action.payload;
    
    if (!payload || typeof payload.articleId !== 'number') {
        return state;
    }
    
    const articleId = payload.articleId;
    let articleState = state[articleId];
        
    // If no state exists for this article we create the initial state by passing undefined
    if (!articleState) {
        // We set the action type to null because we want to step into the default case to create the initial state
        articleState = article(undefined, { type: null });
    }

    return {
        ...state,
        // Now we use our (possible new created) article state and actual action
        [articleId]: article(articleState, action)
    };
};

export default articles;
```
