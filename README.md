[![npm version](https://img.shields.io/npm/v/couple-api.svg)](https://www.npmjs.com/package/couple-api) [![Build Status](https://img.shields.io/travis/bitjson/couple-api.svg)](https://travis-ci.org/bitjson/couple-api) [![Coverage Status](https://img.shields.io/coveralls/bitjson/couple-api.svg)](https://coveralls.io/r/bitjson/couple-api) [![Dependency Status](https://img.shields.io/david/bitjson/couple-api.svg)](https://david-dm.org/bitjson/couple-api)

Couple API
==========

An unofficial Node client and command-line interface for the [Couple App](https://couple.me/) API.

An excellent [web client](https://app.couple.me/) is available for Couple, but it seems to be using a somewhat outdated version of Couple's internal API ("Juliet" Version: `1.40`). For stability and access to the latest features, this library is built around the API methods used by the Couple iOS app ("Juliet" Version: `1.70`).

**WIP**

This library is currently only intended for interaction with the Couple Timeline, and does not include methods for interacting with `Lists`, `Settings`, or the `Calendar`. Please feel free to [open an issue](https://github.com/bitjson/couple-api/issues), if you'd like to see it expanded!

Node Client
-----------

Before interacting with the Couple API, you'll need to authenticate your client instance.

### Authenticate

The `authenticate` method retrieves your Couple App `authObject` using your username and password.

The `authObject` contains both your user profile and the profile of your `other`. It also contains your `authToken`, which is needed to authenticate other API calls. The `authToken` seems to expire at least monthly, as well as when the user's password is changed.

```js
var couple = new Couple();
var email = 'email@example.com';
var password = 'hunter2';
couple.authenticate(email, password, function(err, resObj) {
  if(err) return console.error(err);
  console.log('Client `couple` is authenticated:');
  console.log(couple.identify());
});
```

### WIP: Timeline

All events – messages, stickers, nudges (`Thinking of you...`), etc. – are recorded in the `Timeline`. Upon logging in, the mobile apps begin downloading the timeline, 1600 events at a time, fetching and downloading media as it's encountered.

The `Timeline` function accepts an options object and a callback, and returns a `Timeline` object. The `Timeline` object includes all the information returned by the Couple API, as well as some convenience properties to make consecutive calls easier.

```js
var options = {
  // using defaults:
  // limit: 1600,
  // order: desc,
  // after: ''
};

couple.timeline(options, function(err, timeline) {
  if(err) return console.error(err);
  console.log(timeline);
});
```

#### Moments

Note, the web app (API version `1.40`) has a `/moments` endpoint (not currently implemented in this library), but the mobile apps rely on caching the timeline to generate the `Moments` section.

### Sending

Client API
----------

### Authenticate

```js
couple.authenticate(email, password, callback);
```

Authenticates the `couple-api` instance. The method fetches the users `AuthObject` which contains a variety of information and is needed to authenticate all other calls.

#### Params

| Name       | Description               |
|------------|---------------------------|
| `email`    | The user's email address. |
| `password` | The user's password.      |

#### callback(err, responseObject)

### Identify

```js
couple.identify();
```

Returns an `identify` object.

| Name        | Description                                                                           |
|-------------|---------------------------------------------------------------------------------------|
| `userID`    | The ID (email address) of the authenticated user.                                     |
| `authToken` | The user's base64 encoded authentication token.                                       |
| `otherID`   | The ID (email address) of the other user paired with the authenticated user.          |
| `apiHost`   | The full URL for Couple API server assigned to the user (to which requests are made). |
| `userHash`  | A unique version 4 UUID assigned to the user by Couple.                               |
| `pairHash`  | A unique version 4 UUID assigned to the pair by Couple.                               |

### WIP: Timeline

```js
couple.timeline(options, callback);
```

Returns a segment of the authenticated user's Couple timeline.

#### Options

##### limit

default: `1600`

##### order

default: ascending

##### after

default: `''`

#### Callback(err, timeline)

##### timeline

The `timeline` object contains the `events` returned by the Couple API and additional properties.

```js
// timeline:
{
  'firstItemID': '',
  'lastItemID': '',
  'events': [
    {},
    {},
    ...
  ]
}
```

##### events

The Couple `timeline` contains several types of `events`.

###### text

The text event is a simple message.

```js
{
  "cver": "i1.9.9", // couple version
  "enc": "b64", // text encoding (the iOS app base64 encodes text)
  "eventType": "text", // event type
  "from": "jason@example.com", // email of sender
  "itemID": "timeStampxxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx", // unique identifier – timestamp with concatinated Uuid
  "lID": "#########", // localID – number, seems to be used internally by the mobile apps
  "pairingID": "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx", // the user's pairID
  "text": "dGVzdAo=", // the text, encoded as defined in `enc`
  "timeStamp": 1430522000000, // timestamp in milliseconds
  "decodedText": "test" // `text` decoded by `couple-api`
}
```

CLI (WIP)
=========
