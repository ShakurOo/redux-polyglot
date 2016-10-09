# redux-polyglot

Toolset (actions, reducer, middleware, enhancer, selectors) to help use Polyglot with Redux.

## Installation
```
    npm install --save redux-polyglot
```
(not published yet)

## Setup

First of all, you need the polyglot reducer in your rootReducer :
```javascript
import { createStore, combineReducers } from 'redux';
import { polyglotReducer } from 'redux-polyglot';

const rootReducer = combineReducers({
    ...yourReducers,
    polyglot: polyglotReducer,
});
const store = createStore(rootReducer, {});

```
## Usage

### Set the language
#### without middleware
You can use redux-polyglot without his middleware, for this you need the `setLanguage()` action creator :

- ```setLanguage :: String -> Object -> Action```

Example:
```javascript
import { setLanguage } from 'redux-polyglot';

store.dispatch(setLanguage('en', { yolo: 'yolo' }));
```
second parameter should be `polyglot phrases` (see [polyglot documentation](http://airbnb.io/polyglot.js/))

note: if language phrases already exists, this will overwrite the corresponding object state.

#### with middleware
The `createPolyglotMiddleware()` function allow you to automatically update language and phrases by listening to specific action(s).

The middleware catches specific action(s), and find the locale in the payload, and then [asynchronously] load the `polyglot phrases` (with Promise).

It takes 4 parameters and return a middleware :
- 1 - `actionToCatch :: String | Array<String>`
    - the type(s) of the action to catch
- 2 - `getLocale :: Object -> String`
    - a function that take the catched action as parameter and return new language.
- 3 - `getPhrases :: String -> Promise Object`
    - a function that take the language (as provided by `setLocale`) and return a Promise of Object ( Object should be `polyglot phrases` )

the middleware will catch `actionToCatch`

```javascript
import { createStore, combineReducers, applyMiddleware } from 'redux';

const polyglotMiddleware = createPolyglotMiddleware(
    'ACTION_TO_CATCH',
    action => action.payload.locale,
    locale => new Promise(resolve => {
        // use locale for fetch phrases.
        resolve({
            hello: 'hello'
        });
    }),
)

const store = createStore(rootReducer, {}, applyMiddleware(polyglotMiddleware));
```

you can catch more than one action passing an array of action types: 
```javascript
const polyglotMiddleware = createPolyglotMiddleware(
    ['FIRST_ACTION_TO_CATCH', 'SECOND_ACTION_TO_CATCH'],
    getLocale,
    getPhrases,
)
```

note: if language has not changed, nothing happens.

### Translation
#### with getP() selector
You can use the `getP(state)` selector.

It returns an object with 4 functions inside :
- t: String -> String : translation (the original polyglot `t` function)
- tc: String -> String : translation capitalized
- tu: String -> String : translation upper-cased
- tm: (String -> String) -> String -> String :  translation using a custom morphism

(see [polyglot documentation](http://airbnb.io/polyglot.js/))

#### Getting current locale
`getLocale(state)` selector returns current language. 

#### If you use React

You can use `connect()` from `react-redux`, and the getP() selector, to get the `p` prop in your component.

Proptype: 
````javascript
p: PropTypes.shape({
    t: PropTypes.func.isRequired,
    tc: PropTypes.func.isRequired,
    tu: PropTypes.func.isRequired,
    tm: PropTypes.func.isRequired,
}),
````

##### translate() enhancer
`props.p` can be also be provided easily to a component with the translate enhancer :
```javascript
import { translate } from 'redux-polyglot';
const DummyComponentWithPProps = translate(DummyComponent);
```

##### get locale in a component
You can also use the `getLocale()` selector inside a [mapStateToProps](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) from react-redux.

Proptype: ````locale: PropTypes.string,````

## Team

These folks keep the project moving and are resources for help:

* Jérémy Vincent ([@jvincent42](https://github.com/jvincent42)) - developer
* Jalil Arfaoui ([@JalilArfaoui](https://github.com/JalilArfaoui)) - developer
* Guillaume ARM ([@guillaumearm](https://github.com/guillaumearm/)) - developer
