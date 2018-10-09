#  Async & Await

The async function declaration defines an asynchronous function, which returns an AsyncFunction object. An asynchronous function is a function which operates asynchronously via the event loop, using an implicit Promise to return its result. But the syntax and structure of your code using async functions is much more like using standard synchronous functions.


## Description:

An async function can contain an await expression that pauses the execution of the async function and waits for the passed Promise's resolution, and then resumes the async function's execution and returns the resolved value.

Remember, the await keyword is only valid inside async functions. If you use it outside of an async function's body, you will get a SyntaxError.

        The purpose of async/await functions is to simplify the behavior of using promises synchronously and to perform some behavior on a group of Promises. Just as Promises are similar to structured callbacks, async/await is similar to combining generators and promises.



## Example:
        var resolveAfter2Seconds = function() {
        console.log("starting slow promise");
        return new Promise(resolve => {
            setTimeout(function() {
            resolve(20);
            console.log("slow promise is done");
            }, 2000);
        });
        };

        var resolveAfter1Second = function() {
        console.log("starting fast promise");
        return new Promise(resolve => {
            setTimeout(function() {
            resolve(10);
            console.log("fast promise is done");
            }, 1000);
        });
        };

        var sequentialStart = async function() {
        console.log('==SEQUENTIAL START==');

        // If the value of the expression following the await operator is not a Promise, it's converted to a resolved Promise.
        const slow = await resolveAfter2Seconds();

        const fast = await resolveAfter1Second();
        console.log(slow);
        console.log(fast);
        }

        var concurrentStart = async function() {
        console.log('==CONCURRENT START with await==');
        const slow = resolveAfter2Seconds(); // starts timer immediately
        const fast = resolveAfter1Second();

        console.log(await slow);
        console.log(await fast); // waits for slow to finish, even though fast is already done!
        }

        var stillConcurrent = function() {
        console.log('==CONCURRENT START with Promise.all==');
        Promise.all([resolveAfter2Seconds(), resolveAfter1Second()]).then((messages) => {
            console.log(messages[0]); // slow
            console.log(messages[1]); // fast
        });
        }

        var parallel = function() {
        console.log('==PARALLEL with Promise.then==');
        resolveAfter2Seconds().then((message)=>console.log(message)); // in this case could be simply written as console.log(resolveAfter2Seconds());
        resolveAfter1Second().then((message)=>console.log(message));
        }

        sequentialStart(); // after 2 seconds, logs "slow", then after 1 more second, "fast"
        // wait above to finish
        setTimeout(concurrentStart, 4000); // after 2 seconds, logs "slow" and then "fast"
        // wait again
        setTimeout(stillConcurrent, 7000); // same as concurrentStart
        // wait again
        setTimeout(parallel, 10000); // trully parallel: after 1 second, logs "fast", then after 1 more second, "slow"
        D


### NOTE: If you wish to await two or more promises in parallel, you must still use Promise#then, as parallel does in the example.

## Rewriting a promise chain with an async function

        function getProcessedData(url) {
            return downloadData(url) // returns a promise
                .catch(e => {
                return downloadFallbackData(url)  // returns a promise
                })
                .then(v => {
                return processDataInWorker(v); // returns a promise
                });
        }

        async function getProcessedData(url) {
            let v;
            try {
                v = await downloadData(url); 
            } catch(e) {
                v = await downloadFallbackData(url);
            }
            return processDataInWorker(v); //Implicitly return Promise
        }