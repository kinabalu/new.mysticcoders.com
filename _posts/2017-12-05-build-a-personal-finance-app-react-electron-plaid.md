---
layout: post
title:  "Build a React-based Personal Finance App in Electron with the Plaid Financial API"
date: '2017-12-05T12:00:00.0000Z'
author:
  name: Ryan Baer
  picture: '/assets/blog/authors/rbaer.jpg'
  url: https://mysticcoders.com
  email: baer@mysticcoders.com
comments: true
---
![screenshot](/images/2017-12-05-plaid-preview.png)

This is part 1 of a tutorial series in building a Personal Finance App.
In this series, we'll be building an Electron app that allows you to compute your total balance across multiple bank accounts, using real, live data from your actual accounts.

<!--more-->


To complete this tutorial, you'll need to do the following:

- Have NodeJS installed and setup on your machine
- Sign up for a developer account with Plaid: [Get Started with Plaid](https://dashboard.plaid.com/signup)

The Plaid signup will be covered in more detail as we go.


## What we're building

In this tutorial series, we'll be using Electron to build a small personal finance app by integrating with the Plaid Financial API.
The main behavior of this app will be to aggregate expenses across your accounts so you can easily grep how much you've spent for the month.

Since this is the first post of this series, we'll simply be getting setup and linking one account to display the balance.


### Just Give me the Source
Looking for the completed source for this post? You can clone it from Github:
[Spend Tracker Electron App](https://github.com/kinabalu/spend-tracker)

## The Basics
First, let's review some basics.

### What is Electron?
For those unaware, Electron is a NodeJS framework that allows you to build web apps as desktop apps and integrate with OS-level APIs, just as you would in a native app.

To quote the [Electron website](http://electron.atom.io/) itself:

> *If you can build a website, you can build a desktop app. Electron is a framework for creating native applications with web technologies like JavaScript, HTML, and CSS. It takes care of the hard parts so you can focus on the core of your application.*

This provides the possibility of the elusive ability to "write once, run anywhere". Of course, it can be trickier in implementation, but multiple companies have successfully pulled this off, including Slack, GitKraken, and WhatsApp.

There's a lot more to say about the specifics of Electron. For the sake of this tutorial, here are some basics that are helpful to know:

- Electron is a fork of Chromium. At its core, an Electron app is using a manager process to spawn webpages in separate helper processes, just as Google Chrome does with tabs.
- The manager process is referred to as the main process. Each webpage is referred to as a renderer process and manifests as an actual window in your app.
- Your app will manifest on the user's machine as multiple processes, just as Chrome does. For example, if your app is called Spend Tracker, it will show up in a Process List as:
  - Spend Tracker
  - Spend Tracker Helper

There are often multiple Helper processes running, especially if your app makes use of multiple windows.

### What is Plaid?
Plaid is a FinTech company that provides a modern financial services API. Integrating with Plaid provides your users with the benefits of Plaid's phenomenal UX while handling all of the complexities of managing a user's financial data.

If you've linked a bank account to an app recently (such as on a platform like Coinbase), you may have encountered a nice widget that allows you to easily search for your bank and select an account. This is one of the core offerings of Plaid: the Plaid Link service, which is part of what we will be adding to our personal finance app.

## Up and Running with Electron React Boilerplate
Now that we have the formalities out of the way. Let's dig in.
If you've developed in React before, you're aware that there can be a lot of setup just to get and up and running with everything. This is why tools like Facebook's [create-react-app](https://github.com/facebookincubator/create-react-app) are so useful - they handle all of the setup and allow you to quickly start developing the components that make your app unique.

Since we'll be developing our Electron app using React, it'd be great to have such tooling to handle all of the messy parts of setup.

However, from personal experience, I can tell you that create-react-app doesn't provide you the best development experience with Electron. create-react-app is rather opinionated about its implementation, resulting in situations like their [deliberate lack of support for decorators](https://github.com/facebookincubator/create-react-app/issues/214).

Essentially, create-react-app forces you to work off of a certain folder structure that doesn't work so elegantly with how you'll likely want to structure your Electron app.

Fortunately, there are plenty of Electron React boilerplate repositories out there that handle the majority of the setup for you. For the purposes of this article, we'll use the following starter app:

https://github.com/chentsulin/electron-react-boilerplate

The instructions are in the repository, but all you need to do is:

```bash
git clone --depth=1 https://github.com/chentsulin/electron-react-boilerplate.git spend-tracker
cd $_
npm install
npm run dev
```

Within a few moments, you should see an Electron app pop up. It should have content which is very similar to the popular React demo counter app.

## Minor Tweaks for a Basic UI
Now that we're up and running, let's just make a few quick adjustments for the purposes of our use case.
To start, we simply want this app to display a number. So let's tweak the existing UI to make that happen.

To dig into the code, simply navigate into the app/ folder.

The main page of this boilerplate is stored in `containers/HomePage.js`. However, that page simply renders the `Home` component, stored in `components/Home.js`, which you should open to make the changes.

Navigating to the `render` method of the component we, see:
```html
<div>
  <div className={styles.container}>
    <h2>Home</h2>
    <Link to="/counter">to Counter</Link>
  </div>
</div>
```

We can simply change this to:
```html
<div>
  <div className={styles.container}>
    <h2>Balance</h2>
    <h3>$10,000.55</h3>
  </div>
</div>
```

Great, now you should see the following in the app:
![screenshot](/images/2017-12-05-plaid-ui-tweaks.png)

## Integrating with Plaid Link
Now it's time to integrate with the Plaid service itself. If you haven't already, you'll need to sign up for a Plaid developer account.

### Signup for a Plaid Developer Account
You can sign up for the Plaid developer account here:

[Get Started with Plaid](https://dashboard.plaid.com/signup)

Once you've signed in, you have to request access for developer-level permissions. This level of permissions allows you to access live data, i.e. account balances from your real accounts, up to a certain limit. In this process, Plaid requires that you fill out a little bit of information about your project and your intentions for using their API.

![screenshot](/images/2017-12-05-plaid-signup.png)

Making this request will open a new ticket with the Plaid team via your account.

In the meantime, we can make use of their Sandbox environment to begin integrating.

By registering with Plaid, you should have been provided with three credentials:

- Client ID
- Secret
- Public Key

You can view these keys by navigating to: [Account: Keys](https://dashboard.plaid.com/account/keys)

The intended use case for Plaid is for your client app to communicate with a server you've set up. Your server manages the client ID and secret to prevent exposure through your client app. Your clients then submit the public key in their requests to your server.

The basic Plaid Authentication Flow looks like this:

- A user opens your app and interacts with the Plaid Link widget
- The user selects their institution through Plaid Link and logs in with their bank's credentials, sending the request directly to Plaid's servers.
- Your app receives a callback containing a Public Token to associate with this particular set of credentials.
- The Public Token cannot get the user's account data. In order to request account data, you must dispatch a request for an Access Token to Plaid with the Public Token, your client ID, and your app's secret.
- Once the Access Token has been retrieved, your app can fetch account data for the selected institution directly from Plaid.

### Important Note: This App is Intended for Personal Use Only
In the above description of the authentication flow, you may have noticed that a server is involved. However, for the purposes of this tutorial, rather than deal with setting up a server, it's easier to just have everything embedded in the app.

This is not Plaid's intended usage for working with their API. If you were to distribute your app with your client ID and secret included, it would be easy for others to pull out your credentials and make free use of them.

What we're building here is a local, non-distributable app for demo purposes. Should you want to build this into something distributable, some options might be:

- The obvious: set up a server that integrates with Plaid in the intended way
- Bring Your Own Credentials: Constrain your userbase to technical users, and provide the ability to enter client ID and secret into the app, with the promise that you don't ship their credentials off anywhere. Naturally, your app had better be open-source if you want to pull off something like this.

Having said that, as a reader of this post, you're most likely technically-minded; so you can simply build the app for yourself and use it as you see fit.

### What is Plaid Link?
![screenshot](/images/2017-12-05-plaid-link.png)

Plaid Link is a component of Plaid's service that provides a drop-in module with a secure, elegant authentication flow for each institution that Plaid supports. ([Source](https://plaid.com/docs/transition-guide/))

It's a pretty sleek interface, so let's get it into our app.

### Plaid Setup and Handling Storage
In order to work with Plaid, we're going to need to install two dependencies: `plaid`, and `react-plaid-link`.

The first, `plaid`, is Plaid's official npm package for interacting with their API.
The second, `react-plaid-link`, allows us to easily integrate Plaid Link into our app as a React component.

So, let's install them:

```bash
npm install plaid react-plaid-link
```

With our dependencies installed, we can add our Plaid developer credentials into the app.
As mentioned above, this is not how you would do it for a production-ready app.

First, we'll open up `components/Home.js`.

At the top of the file, let's import the two libraries we just installed:
```javascript
import plaid from 'plaid';
import PlaidLink from 'react-plaid-link';
```

Next, let's place our credentials into the app as constants:
```javascript
const CLIENT_ID = 'your-client-id';
const SECRET = 'your-secret';
const PUBLIC_KEY = 'your-public-key';
```

After that, we'll create a new constructor for the component to initialize the Plaid client and setup our initial state:

```javascript
export default class Home extends Component {
  constructor(props) {
    super(props);

    this.state = {
      items: getItems(),
      balance: 0,
      loading: false,
      error: null,
    };

    this.client = new plaid.Client(
      CLIENT_ID,
      CLIENT_SECRET,
      PUBLIC_KEY,
      plaid.environments.sandbox,
    );
  }
```

Before we continue, let's talk a little bit about how we're going to track information from Plaid.

In Plaid's terminology, an **Item** is a link to a user's institution. As mentioned above, a Public Token is returned when a user links an institution, and we send that Public Token to Plaid in order to get an Access Token to retrieve account data.

For this first post, we're only going to calculate the balance from one institution, but we should set things up in a way such that there's not too much friction to add support for multiple institutions later.

Since we're literally working in a webpage, we'll make use of `localStorage` for the purpose of tracking what institutions the user has linked.

Here's the structure we'll use to track institutions in `localStorage`:
```javascript
{
  items: {
    byId: {
      ins_9: {
        id: "ins_9",
        name: "Capital One",
        publicToken: "public-token",
        accessToken: "access-token"
      }
    },
    allIds: [
      "ins_9"
    ]
  }
}
```

In the above structure, we maintain an array of all IDs, and an object with the individual IDs as keys. This is a convenience that allows us to easily work with the data in either format.

`localStorage` only stores strings, so we'll be serializing and deserializing our objects to work with them. As such, it makes sense to define a few helper methods.

First, in `localStorage`, we'll refer to our data through a key, `items`. As a good practice, let's create a `const` to represent that key.
```javascript
const KEY_ITEMS = 'items';
```


Next, let's define the method that will retrieve our items from `localStorage`. Additionally, the method initializes the object in `localStorage` if it's not already set.

```javascript
const getItems = () => {
  let items = JSON.parse(localStorage.getItem(KEY_ITEMS));
  if (!items) {
    items = {
      byId: {},
      allIds: [],
    };
    setItems(items);
  }

  return items;
};
```

Then, we'll make a method, `setItems`, which simply updates the object:
```javascript
const setItems = items => {
  localStorage.setItem(KEY_ITEMS, JSON.stringify(items));
};
```

Finally, we'll make a method `addItem` which makes use of both previous methods to add a new item to the object:
```javascript
const addItem = item => {
  const oldItems = getItems();
  if (oldItems.byId[item.id]) {
    return;
  }

  const items = {
    byId: {
      ...oldItems.byId,
      [item.id]: item,
    },
    allIds: [...oldItems.allIds, item.id],
  };

  setItems(items);
};
```

The above method ensures that we dont add duplicate keys by checking if the ID is already in the object, and returns immediately if it is.

With these methods in place, we can now get to work at configuring our component to work with Plaid Link.


### Integrating Plaid Link

The `react-plaid-link` component renders in our app as a button that the user can click to login to their institution through Plaid.

Our first changes will take place in the `render` method. As a reminder, this is what the method currently looks like:

```jsx
render() {
  return (
    <div>
      <div className={styles.container}>
        <h2>Balance</h2>
        <h3>$10,000.55</h3>
      </div>
    </div>
    )
  }
```

Now let's update it to reflect the component's state and any institutions stored in `localStorage`.

First, in the beginning of the `render` method, we'll retrieve any state relevant to the UI, and also retrieve the `items` from `localStorage`:

```javascript
const { balance, loading, error } = this.state;
const items = getItems();
```

Next, we know we want to display different messages in place of the balance depending on the state, so let's write those conditions:


```javascript
let value;
if (loading) {
  value = 'Loading...';
} else {
  value = `$${balance}`;
}
```

We create a variable, `value`, which has a value of `'Loading...'` if the component's state is loading, and otherwise displays the current balance. If the balance hasn't been computed, the component's initial state has the balance set to $0.

Now, let's make some tweaks to how elements actually get rendered, and include the `PlaidLink` component. We'll look at the whole chunk and then break it up:

```jsx
return (
  <div>
    <div className={styles.container}>
      {!error && (
        <div>
          <h1>Balance</h1>
          <h2>{value}</h2>
        </div>
      )}
      {error && <h3>{error}</h3>}
      {items.allIds.length < 1 && (
        <div>
          <h3>Link to your bank to view your account balance.</h3>
          <PlaidLink
            publicKey={PUBLIC_KEY}
            product="auth"
            env="sandbox"
            apiVersion={'v2'}
            clientName="Spend Tracker"
            onSuccess={this.onItemLinked.bind(this)}
          />
        </div>
      )}
    </div>
  </div>
);

```

First, we only display the balance if there's no error (i.e. error is *falsy*). If there is an error, we display the error message instead.
```jsx
{!error && (
  <div>
    <h1>Balance</h1>
    <h2>{value}</h2>
  </div>
)}
{error && <h3>{error}</h3>}
```

And then, we only want to render the `PlaidLink` component if the user hasn't linked any accounts yet:
```jsx
{items.allIds.length < 1 && (
  <div>
    <h3>Link to your bank to view your account balance.</h3>
    <PlaidLink
      publicKey={PUBLIC_KEY}
      product="auth"
      env="sandbox"
      apiVersion={'v2'}
      clientName="Spend Tracker"
      onSuccess={this.onItemLinked.bind(this)}
    />
  </div>
)}
```

The parameters to PlaidLink are documented on the repository. Here's what's important to note:

- `product` is important - its values are essentially scopes of behavior for accessing Plaid. We're requesting `auth` because it allows us to view all accounts and overall balances. If we wanted to view individual transactions, we'd have to request `transactions` as well.
- `env` is set to `sandbox`. Once your developer account is approved, it needs to be changed to `development` to access your real financial data.
- `onSuccess` is the method that gets called once the user successfully logs into and links an institution. The callback, which we'll go into next, is the starting point for requesting account data.

### Handling Data From Plaid
Before we can successfully run the app, we have to define how our app responds once the user has linked an account.

As mentioned above, the first thing we need to do is define our `onSuccess` method, which will be named `onItemLinked`:
```javascript
onItemLinked(publicToken, metadata) {
  const { institution_id: id, name } = metadata.institution;

  this.client
    .exchangePublicToken(publicToken)
    .then(res => {
      const { access_token: accessToken } = res;
      addItem({
        id,
        name,
        publicToken,
        accessToken,
      });

      this.fetchBalance();
    })
    .catch(() => {
      this.setState({
        ...this.state,
        error: 'Unable to authenticate with service',
      });
    });
}
```

The method receives two parameters, `publicToken` and `metadata`. `publicToken` is all that we need to get an `accessToken`, but `metadata` contains information about the institution, such as name and ID, that we want to track.

Using the destructuring alias syntax, we access `institution_id` and store it as a variable, `id`, as well as `name`, from `metadata.institution`.

Now, we need to make another request. This is the request that allows us to exchange our `publicToken` for an `accessToken`. Using the Plaid client that we setup earlier, we invoke `exchangePublicToken`, which returns a Promise.

From the response, we can easily grab the `accessToken`.

It's at this point that we invoke our storage helper method, `addItem`. This synchronizes all the pertinent information about the user's linked financial institution, including the tokens. You might remember that Plaid refers to this as an `Item`.

Finally, we invoke another method, `fetchBalance`, which makes use of the `accessToken` to retrieve the total balance.


```javascript
fetchBalance() {
  const items = getItems();

  if (items.allIds.length > 0) {
    const item = items.byId[items.allIds[0]];
    const { accessToken } = item;
    this.setState({ ...this.state, loading: true });
    this.client
      .getBalance(accessToken, {})
      .then(res => {
        const balance = res.accounts.reduce((val, acct) => val + acct.balances.available, 0);
        this.setState({ ...this.state, balance, loading: false });
      })
      .catch(() => {
        this.setState({
          ...this.state,
          loading: false,
          error: 'Unable to get balance.',
        });
      });
  }
}
```

This method also needs to be invoked from `componentWillMount`:
```javascript
componentWillMount() {
  this.fetchBalance();
}
```

This ensures that, if the user reopens the app after linking an account, it can simply proceed to fetching the user's balance.


Diving into the `fetchBalance` method, we first retrieve the `items` from `localStorage`. Since we invoke this method from `componentWillMount`, it's possible that the user has not linked any institutions, and so we don't want to proceed if there's nothing to request from yet.

Once we're sure that they've linked an institution, we simply get the first institution in the list.
This is because, for the purposes of this post, we're only focused on working with one institution right now. Later in this series, we'll look into managing multiple institutions.

Now that we have the institution, we can simply retrieve the `accessToken` from the item and invoke the Plaid method `getBalance`.

Once we've received a response, we use `Array.prototype.reduce` on the `accounts` array to sum up the available balances across all accounts and update the component's state to reflect the final amount.

And of course, if there is any issue that raises an error, we update the state to indicate that something went wrong.


## That's All
And that's it for Part 1. To recap, we covered quite a lot in this post:

- Getting setup with Electron React boilerplate
- Understanding Plaid and how to integrate it into an app
- How to manage data from Plaid using `localStorage`.
- Integrating Plaid and Plaid Link into our component to successfully retrieve data from an institution
- Calculating the sum of balances across all of the user's accounts

In Part 2, we'll dig into:

- Linking and viewing multiple institutions
- Removing institutions
- Adding a refresh button
- Adding a filter to toggle different institutions

If you have any questions, don't hestitate to reach out in the comments.
