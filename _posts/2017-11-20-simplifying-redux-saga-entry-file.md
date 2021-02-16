---
layout: post
title:  "Simplifying the Redux Saga entry file"
date: '2017-11-20T06:00:00.0000Z'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
comments: true
---

<img src="/images/blue-sunflower.jpg" border="0" />
<br />
<small><a href="https://www.flickr.com/photos/georigami/16396244361/in/photolist-qYT3di-WiwJz6-9CJa2N-koFtcM-SthJiZ-9pExHC-NHRHp-Wix3fX-WrzH8j-nKTsja-gYBjKA-ie3sc5-G9PFsp-8kFEyR-roM1Sn-21yurkP-aFW3wZ-dvbogZ-e8pzk1-8W7c3g-9ejuS3-9EHXgY-VUUiHq-aaBbBG-21yuq9R-n7wrUX-cigvj7-dxt8Tn-av8vUh-icWyTi-p2xRyG-iRFn1J-21yuqPt-nQcYbU-dFGEar-fdgHPx-XTfFfR-qn9LSP-ie2DUA-p2AFNT-BZDiAg-Zd7Pmi-Zd7NBH-6CfmoK-dd57Ps-8w2UCf-qVJKmb-rfwKnn-pJqnq3-rA2xMC">From georigami on flickr</a></small>

My entry file with Redux Saga generally looks like this:

```javascript
import {all} from "redux-saga/effects";

import {
    watchLoginRequest,
    watchLogoutRequest,
    watchCurrentUserRequest,
    watchUpdateProfileRequest
} from "./authSaga";

import {
    watchGetUserList,
    watchGetUser,
    watchAddUser,
    watchUpdateUser,
    watchDeleteUser
} from "./adminSaga";

export default function* rootSaga() {
    yield all([
        watchLoginRequest(),
        watchLogoutRequest(),
        watchCurrentUserRequest(),
        watchUpdateProfileRequest(),
        watchGetUserList(),
        watchGetUser(),
        watchAddUser(),
        watchUpdateUser(),
        watchDeleteUser(),
    ]);
}
```

As you can see a set of imports and then an array of functions added to the generator function used in my store module. This works fine, but I often find that swapping between a specific sagas file and the rootSagas file to be an unnecessary step prone to error. 

<!--more-->

In the individual saga, these list of functions that get called are pointing to non-blocking yields to handle the action types spawned from the action creators that in turn will call a generator function to handle that action type:

```javascript
export function* watchLoginRequest() {
    yield* takeEvery(types.LOGIN_REQUEST, loginRequest);
}
```

These work fine, can be organized near the end of your individual sagas files, but the way I had it setup, contained too many places to edit and possibly miss. 

I’ve taken the liberty of using some ES7 magic to fix my pain.  Here’s the new `rootSaga.js`:

```javascript
import {all} from "redux-saga/effects";
import { sagas as authSagas } from "./authSaga";
import { sagas as adminSagas } from "./adminSaga";

export default function* rootSaga() {
    yield all({
        ...authSagas,
        ...adminSagas,
    });
}
```

I like this a lot more, and when you have more than one saga, you can see how we just use the spread operator to combine things nicely. As an example in one of our saga files, here’s how we can handle our watch generator functions:

```javascript
export const sagas = {
    watchCurrentUserRequest: takeEvery(types.CURRENT_USER_REQUEST, currentUserRequest),
    watchUpdateProfileRequest: takeEvery(types.UPDATE_PROFILE_REQUEST, updateProfileRequest),
    watchLoginRequest: takeEvery(types.LOGIN_REQUEST, loginRequest),
    watchLogoutRequest: takeEvery(types.LOGOUT_REQUEST, logoutRequest),
};
```

Can you see any way to inject any further brevity into the above?  This pattern is common in a lot of our teams redux-saga code, and I like the way this looks for refactoring.