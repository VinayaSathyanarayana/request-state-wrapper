# Request State Wrapper

A small, less than 1kb gzipped, utility wrapper for Asynchronous JavaScript events.  

It allows you to easily handle fetching, stalled and finished states.

<p align="center"><img src="https://user-images.githubusercontent.com/12635736/55130897-ab155600-5170-11e9-9d83-478281e512b7.gif" alt="" /></p>

## Philosophy

The way we handle loading states in front end applications can be incredibly reptetive and imperative.

We also design loading states that aren't always the optimal user experience for all users. This means users with fast **AND slow connections**.

Request State Wrapper aims to solve the following problems.

- Make it easy to detect stalled loading states so we can communicate to users on slower connections that the app is still working / not broken.
- Make it easy to batch multiple requests together to avoid staggered UI updates.
- Take the reptetive code required to handle loading states, and compose it into a single, declarative utility function. This helps keep our codebases DRY(Dont Repeat Yourself).

## Usage

```bash
npm install --save request-state-wrapper
```

```javascript
import { createRequest } from 'request-state-wrapper';

const yourAsyncFunction = () => 
    fetch('https://api.github.com/repos/nodejs/node').then(response => response.json());

// Create your request with request-state-wrapper
const request = createRequest({
    request: yourAsyncFunction,
    stalledDelay: 1000,
    onStalled: () => { /* handle stalled state */ },
});

// Run it!
request()
  .then(response => { /* handle request response */ })
  .catch(error => { /* handle request error */ });
```

### Usage recipies

For more detailed implementation recipies see:

- [Vanilla JavaScript with explicit event handlers](https://codesandbox.io/s/62vpv1o813)
- [React Hooks](https://codesandbox.io/s/kw9p9jwz3v)
- [Reusable React provider component](https://codesandbox.io/s/w6m4w2vowl)
- [React with Redux](https://codesandbox.io/s/8krrvvj2z9)

## API

`createRequest` takes a single object argument.

```javascript
const demoRequest = createRequest({ 
    request,
    stalledDelay,
    onStateChange,
    onFetching,
    onStalled,
    onFinished,
 })
```

**Required**

- `request` - Function that returns a promise *or* Array of functions that return promises

**Optional**

- `stalledDelay` - Time, in milliseconds, it takes for the request to be considered stalled (defaults to 1000)
- `onStateChange` - Handler function that is called whenever the request state changes
- `onFetching` - Handler function that is called whenever the request starts fetching
- `onStalled` - Handler function that is called whenever the request state becomes stalled
- `onFinished` - Handler function that is called whenever the request state finishes

`createRequest` returns a function that invokes the request. It takes a single argument, an object of options *all of which are optional*.

```javascript
demoRequest({
    onStateChange,
    onFetching,
    onStalled,
    onFinished,
 })
```

- `onStateChange` - Handler function that is called whenever the request state changes
- `onFetching` - Handler function that is called whenever the request starts fetching
- `onStalled` - Handler function that is called whenever the request state becomes stalled
- `onFinished` - Handler function that is called whenever the request state finishes

**Important:** 

- Any options declared at request time will override options declared at creation time
- `onFetching`, `onStalled`, `onFinished` take precedence over `onStateChange`
