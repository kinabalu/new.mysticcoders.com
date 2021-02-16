---
layout: post
title:  "React to CORS with an Express Proxy"
date: '2020-05-01T12:00:00.0000Z'
coverImage: '/assets/blog/chris-panas-0Yiy0XajJHQ-unsplash.jpg'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
comments: true
---

![](/assets/blog/chris-panas-0Yiy0XajJHQ-unsplash.jpg)

If you're building any application using web technology destined for a browser it will likely need to interact with a third party API. Web development has taken a lot of the wonderful patterns from backend development we've all enjoyed and moved it to the frontend. With a server-based backend we would be looking at access to a persistence infrastructure possibly directly (though that isn't the best practice), or through a first-party API with a pattern of controls based on REST or GraphQL. With front-end JavaScript frameworks in order to move beyond mostly a toy, you'll be accessing data through a first-party or a third-party API.

<!--more-->

> Third party APIs are application programming interfaces provided by third parties with systems outside of your immediate control like Twitter, Google or Amazon. They allow you to access a curated set of functionality via typically REST or GraphQL or similar. They allow for retrieving applicable data or mutating state stored in these systems.

The difference between a first-party API and a third-party API is one of origin, the same origin identifies access controls which let you access anything at the same host origin. As long as you are sticking with first-party APIs you will never have any further issues. For a lot of applications, this works wonderfully. 

## The Joy of 3rd party APIs
CORS. Until you've had to talk with a third-party API you may have been unaware of this wonderful beast. Today is the day, and a simple NodeJS proxy might be the way. First we probably need to quickly define what it is.

> **Cross-origin resource sharing**
> 
> Cross-origin resource sharing is a mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served. A web page may freely embed cross-origin images, stylesheets, scripts, iframes, and videos.

So if you're looking for access using a library like [Axios](https://github.com/axios/axios) or [Fetch](https://javascript.info/fetch) and using the same host, no problem. If however you're trying to access let's say, a user's geo location using their IP

hey ho

let's go

## Pre-flight Requests
Discuss pre-flight requests, the OPTIONS header that gets passed

## Browser-based Options
If you're testing locally...

### Firefox
  ![](https://addons.cdn.mozilla.net/static/img/addon-icons/default-64.png)

  Firefox has a great plugin / extension system and the best way to disable CORS for when you're testing your web application locally is to use [CORS Everywhere](https://addons.mozilla.org/en-US/firefox/addon/cors-everywhere/). During normal / regular browsing you should strive to keep this off.

### Google Chrome
  ![](https://lh3.googleusercontent.com/Jgrc_x59UKuesuygHMfaBNKoLiO2uLzvTx-ssa0xLeuradaZhwfjWVos_l6w6kf5SRSnRiwy2eA=w128-h128-e365)

  The easiest way to disable the CORS restrictions is to install the plugin [Allow CORS: Access-Control-Allow-Origin](https://chrome.google.com/webstore/detail/allow-cors-access-control/lhobafahddgcelffkeicbaginigeejlf). Make sure to turn it off during regular browsing as it can cause some unforeseen issues.

### Safari
No plugin necessary. Easiest way is to enable the developer menu from *Preferences -> Advanced* and under the Develop menu select *Disable Cross-Origin Restrictions*. Reload the page.

## Build a NodeJS proxy server
Let's build a proxy server which will let us pass the necessary headers

```javascript
const express = require('express')
const request = require('request')
const cors = require('cors')
```


```javascript
const app = express()

const corsOptions = {
    origin: (origin, callback) => {
        callback(null, true)
    },
}
app.options('*', cors(corsOptions))

const PRODUCTION_URL = 'https://virustrack.live'
const STAGING_URL = 'https://staging.virustrack.live'
const TEST_URL = 'https://test.virustrack.live'

const environment = process.env.NODE_ENVIRONMENT ? process.env.NODE_ENVIRONMENT : "production"


const url = environment === "production" ? PRODUCTION_URL : environment === "staging" ? STAGING_URL : TEST_URL

app.use('/', cors(corsOptions), (req, res) => {
    const request_url = `${url}${req.url}`

    req.pipe(request(request_url)).pipe(res)
});

app.listen(process.env.PORT || 3100)
```