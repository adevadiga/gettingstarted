# Promise:

A Promise is an object representing the eventual completion or failure of an asynchronous operation.

Essentially, a promise is a returned object to which you attach callbacks, instead of passing callbacks into a function.

One of the great things about using promises is chaining.

## Chaining
A common need is to execute two or more asynchronous operations back to back, where each subsequent operation starts when the previous operation succeeds, with the result from the previous step. We accomplish this by creating a promise chain.

Here's the magic: the then() function returns a new promise, different from the original:

        doSomething(function(result) {
            doSomethingElse(result, function(newResult) {
                doThirdThing(newResult, function(finalResult) {
                console.log('Got the final result: ' + finalResult);
                }, failureCallback);
            }, failureCallback);
        }, failureCallback);

        doSomething().then(function(result) {
            return doSomethingElse(result);
        })
        .then(function(newResult) {
            return doThirdThing(newResult);
        })
        .then(function(finalResult) {
            console.log('Got the final result: ' + finalResult);
        })
        .catch(failureCallback);

        Or using Arrow functions

        doSomething()
            .then(result => doSomethingElse(result))
            .then(newResult => doThirdThing(newResult))
            .then(finalResult => {
                console.log(`Got the final result: ${finalResult}`);
            })
            .catch(failureCallback);

## Chaining after a catch

It's possible to chain after a failure, i.e. a catch, which is useful to accomplish new actions even after an action failed in the chain. Read the following example:

        new Promise((resolve, reject) => {
        console.log('Initial');

        resolve();
        })
        .then(() => {
            throw new Error('Something failed');
                
            console.log('Do this');
        })
        .catch(() => {
            console.log('Do that');
        })
        .then(() => {
            console.log('Do this, no matter what happened before');
        });

        Initial
        Do that
        Do this, no matter what happened before


## Error propagation

    Basically, a promise chain stops if there's an exception, looking down the chain for catch handlers instead. This is very much modeled after how synchronous code works:

    Note that if there is any exception promise chain stops and looks down for corresponding catch handler.
    What if there is no catch??????

    =>>>There will be no trace of error. Error is vapourised.

    Promises solve a fundamental flaw with the callback pyramid of doom, by catching all errors, even thrown exceptions and programming errors. This is essential for functional composition of asynchronous operations.




##  Description
A Promise is a proxy for a value not necessarily known when the promise is created. It allows you to associate handlers with an asynchronous action's eventual success value or failure reason. This lets asynchronous methods return values like synchronous methods: instead of immediately returning the final value, the asynchronous method returns a promise to supply the value at some point in the future.

A Promise is in one of these states:

pending: initial state, neither fulfilled nor rejected.
fulfilled: meaning that the operation completed successfully.
rejected: meaning that the operation failed.

A pending promise can either be fulfilled with a value, or rejected with a reason (error). 
When either of these options happens, the associated handlers queued up by a promise's then method are called. 


### Promise.all(iterable)
    Returns a promise that either fulfills when all of the promises in the iterable argument have fulfilled or rejects as soon as one of the promises in the iterable argument rejects. If the returned promise fulfills, it is fulfilled with an array of the values from the fulfilled promises in the same order as defined in the iterable. If the returned promise rejects, it is rejected with the reason from the first promise in the iterable that rejected. This method can be useful for aggregating results of multiple promises.

### Promise.race(iterable)
    Returns a promise that fulfills or rejects as soon as one of the promises in the iterable fulfills or rejects, with the value or reason from that promise.

### Promise.reject(reason)
    Returns a Promise object that is rejected with the given reason.
    
### Promise.resolve(value)
    Returns a Promise object that is resolved with the given value. If the value is a thenable (i.e. has a then method), the returned promise will "follow" that thenable, adopting its eventual state; otherwise the returned promise will be fulfilled with the value. Generally, if you don't know if a value is a promise or not, Promise.resolve(value) it instead and work with the return value as a promise.

## Prototypes:
### Promise.prototype.catch(onRejected)
Appends a rejection handler callback to the promise, and returns a new promise resolving to the return value of the callback if it is called, or to its original fulfillment value if the promise is instead fulfilled.

### Promise.prototype.then(onFulfilled, onRejected)
Appends fulfillment and rejection handlers to the promise, and returns a new promise resolving to the return value of the called handler, or to its original settled value if the promise was not handled (i.e. if the relevant handler onFulfilled or onRejected is not a function).

###Promise.prototype.finally(onFinally)
Appends a handler to the promise, and returns a new promise which is resolved when the original promise is resolved. The handler is called when the promise is settled, whether fulfilled or rejected.


###########################
# Gotchas
Normally use Error while rejecting

        const promise = new Promise((resolve, reject) => {
        //throw new Error("Error thrown");
        reject("Rejecting promise"); //Should be  reject(new Error("Some cause"));
        }).then(
        (result) => {
            console.log(result);
        }
        ).catch((err) => {
        //console.log("Caughte here....");
        console.log(err);
        }); 