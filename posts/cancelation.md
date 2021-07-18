---
title: Cancellation date:
---

There are many cases we keep a handler in order to cancel an operation. Maybe the most familiar example is keeping the
timeout object returned by the `setTimeout` function in order to clear it.

Let's start with an example. Somewhere in my code I created a timeout trap:

```typescript
const timer = setTimeout(() => {
    throw new Error('timeout');
}, 5000);
```

Later, if my process went well, and I want to cancel my trap, I need:

1. Access the `timer` object
2. Implement the cancellation

While the first requirement is mostly mandatory (there will be another snippet about context juggling), the second one
require knowledge about the creation of the timer - and therefore - a coupling.

In order to remove this coupling, a function creates side effect, should return a handler for it.

Consider the following timeout function:

```typescript
function timeout(seconds: number): () => void {
    const timer = setTimeout(() => {
        throw new Error('timeout');
    }, seconds * 1000);

    return () => clearTimeout(timer);
}
```

This implementation creates the timer, and the cancellation function in the same context. Canceling timeout
created by this function require no more than calling a function.

Real world example for the timeout function:

```typescript

function action(opts: { url: string, timeout?: number }) {
    // set timeout trap (options or default 5 seconds)
    const cancel = timout(opts.timeout || 5);

    // make a reuest
    return request(opt.url)
        .then(res => {
            // handle response and return 
        })
        .cache(err => {
            // handle error
        })
        .finally(cancel); // remove trap
}
```

Another use for cancellation functions is for graceful terminating applications. 
But this is a topic for another snippet.

