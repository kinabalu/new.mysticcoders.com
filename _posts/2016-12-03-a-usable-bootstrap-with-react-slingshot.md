---
layout: post
title:  "A Usable Bootstrap With React Slingshot"
date: '2016-12-03T11:11:00.0000Z'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
comments: true
---
<svg version="1.1" id="Layer_2" width="300" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	viewBox="0 0 600 600" xml:space="preserve">
<circle fill="#00D8FF" cx="299.529" cy="299.628" r="50.167"/>
<path fill="none" stroke="#00D8FF" stroke-width="24" stroke-miterlimit="10" d="M299.529,197.628
	c67.356,0,129.928,9.665,177.107,25.907c56.844,19.569,91.794,49.233,91.794,76.093c0,27.991-37.041,59.503-98.083,79.728
	c-46.151,15.291-106.879,23.272-170.818,23.272c-65.554,0-127.63-7.492-174.29-23.441c-59.046-20.182-94.611-52.103-94.611-79.559
	c0-26.642,33.37-56.076,89.415-75.616C167.398,207.503,231.515,197.628,299.529,197.628z"/>
<path fill="none" stroke="#00D8FF" stroke-width="24" stroke-miterlimit="10" d="M210.736,248.922
	c33.649-58.348,73.281-107.724,110.92-140.48c45.35-39.466,88.507-54.923,111.775-41.505
	c24.248,13.983,33.042,61.814,20.067,124.796c-9.81,47.618-33.234,104.212-65.176,159.601
	c-32.749,56.788-70.25,106.819-107.377,139.272c-46.981,41.068-92.4,55.929-116.185,42.213
	c-23.079-13.31-31.906-56.921-20.834-115.233C153.281,368.316,176.758,307.841,210.736,248.922z"/>
<path fill="none" stroke="#00D8FF" stroke-width="24" stroke-miterlimit="10" d="M210.821,351.482
	c-33.746-58.292-56.731-117.287-66.312-166.255c-11.544-58.999-3.382-104.109,19.864-117.566
	c24.224-14.024,70.055,2.244,118.14,44.94c36.356,32.28,73.688,80.837,105.723,136.173c32.844,56.733,57.461,114.209,67.036,162.582
	c12.117,61.213,2.309,107.984-21.453,121.74c-23.057,13.348-65.249-0.784-110.239-39.499
	C285.567,460.886,244.898,410.344,210.821,351.482z"/>
</svg>

Getting started with [React](https://facebook.github.io/react/) can be a bit daunting, and as with using things like AngularJS I've found that using a bootstrap is a great way to start. With that in mind, I've chosen the wonderful [react-slingshot](https://github.com/coryhouse/react-slingshot) from [@housecor](https://twitter.com/housecor). It contains a demo which can be removed with an `npm script` and from which we'll extract what I feel is most useful including: integration with redux, react-router, and redux-thunk.

Most [React](https://facebook.github.io/react/) repositories seem to dispense with build tools and instead use a mixture of npm scripts and webpack to achieve what it needs to achieve. Just recently a new package manager came out to unseat the stalwart in `npm`, we're using [yarn](https://yarnpkg.com), please follow the install instructions for your particular environment.
<!--more-->
Clone the `react-slingshot` repository using the following `git` command:

```shell
% git clone git@github.com:coryhouse/react-slingshot.git
```

First thing we'll do after cloning `react-slingshot` locally is remove the demo as it isn't useful any longer

```shell
yarn remove-demo
```

## Main entrypoint

Let's add the necessary items for using redux and integrating react-router. Open up the `src/index.js` and paste the following in:

```javascript
/* eslint-disable import/default */
import React from 'react';
import {render} from 'react-dom';
import { Provider } from 'react-redux';
import { Router, browserHistory } from 'react-router';
import { syncHistoryWithStore } from 'react-router-redux';

import routes from './routes';
import configureStore from './store/configureStore';

const store = configureStore();
const history = syncHistoryWithStore(browserHistory, store);

render(
  <Provider store={store}>
    <Router history={history} routes={routes} />
  </Provider>,
  document.getElementById('app')
);
```

Let's take this in chunks, the eslint bit is because there's no default export in this file and we want eslint to ignore that. Then we have imports for bringing in react, redux, and the integration with react-router and redux.

```javascript
import React from 'react';
import {render} from 'react-dom';
import { Provider } from 'react-redux';
import { Router, browserHistory } from 'react-router';
import { syncHistoryWithStore } from 'react-router-redux';
```

The bootstrap already contains a working version to configure the store, and has the integration with `redux‐thunk` for the middleware. We call the exported function to configure the store, and then `syncHistoryWithStore(browserHistory, store)` will save our router state in the redux store.

```javascript
import routes from './routes';
import configureStore from './store/configureStore';

const store = configureStore();
const history = syncHistoryWithStore(browserHistory, store);
```

This snippet is the entrypoint for our application. The `Provider` component is offered by `redux` and allows us to wrap all other components with access to our store, and inside that we add the `Router` component with access to our store-saved history, and the routes which we will define in the next section. That's it for our index module, let's move on to defining some simple routing.

```javascript
render(
  <Provider store={store}>
    <Router history={history} routes={routes} />
  </Provider>,
  document.getElementById('app')
);
```

## Routes
Now we'll define our routes module which was referenced in the index file. Create a new file in `src/routes.js` and paste the following in.

```javascript
import React from 'react';
import { Route, IndexRoute } from 'react-router';

import HomePage from './pages/HomePage';

export default (
  <Route path="/">
    <IndexRoute component={HomePage}/>
  </Route>
);
```

This is a typical [react-router](https://github.com/ReactTraining/react-router) file, and you can browse the documentation to find more ways to customize things.

## Pages
You may have notice we've identified another missing file `src/pages/HomePage.js`, which we will create now.

```javascript
import React from 'react';

const HomePage = () => {
  return (
    <h1>Hello, World!</h1>
  );
};

export default HomePage;
```

This is the simplest of components, a functional stateless component which merely outputs Hello, World! wrapped in an H1 tag. If you attempt to run things now you will still receive errors in the reducers directory. While out of the scope of this quick tutorial, reducers are how an application's state changes in response to something happening, you can [read more in the Redux docs](http://redux.js.org/docs/basics/Reducers.html).

Let's open `src/reducers/index.js` and modify with the following:

```javascript
import { combineReducers } from 'redux';
import {routerReducer} from 'react-router-redux';

const rootReducer = combineReducers({
  routing: routerReducer,
});

export default rootReducer;
```

Remember, we added `react-router-redux` to our index module, so as our first reducer we need to at the routerReducer to the list of reducers offered up in the `combineReducers` call. It's in this file that you can define more reducers to be included in your `redux` stack.

## Fin

With that, browse to http://localhost:3000 and see the familiar "Hello, World!". There is so much more to be done, we can dip into the `redux` portion and start defining actions, action creators, reducers, and use `redux-thunk` for the inevitable need for asyncronous calls in our action creators. This is a first step, stay tuned and we'll dig even deeper into the React / Redux stack.

And if you weren't in Sofia, Bulgaria for [CodeMonsters](http://codemonsters.pro/)/[Java2Days](http://2016.java2days.com) this year, my presentation on [React and Redux](http://www.slideshare.net/kinabalu/react-and-redux) is available on slideshare. Thanks to everyone who was in attendance,
