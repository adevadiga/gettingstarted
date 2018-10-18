Debugging node js locally with chrome

1. Start node process
        node --inspect app.js

2. Open chrome.
        chrome://inspect/

https://medium.com/@paul_irish/debugging-node-js-nightlies-with-chrome-devtools-7c4a1b95ae27

https://nodejs.org/en/docs/guides/debugging-getting-started/


2. Event loop in Node.js.


Threads in node:

Node is fast when the work associated with each client at any given time is "small".

This applies to callbacks on the Event Loop and tasks on the Worker Pool.

Node uses a small number of threads to handle many clients. In Node there are two types of threads: one Event Loop (aka the main loop, main thread, event thread, etc.), and a pool of k Workers in a Worker Pool (aka the threadpool).

If a thread is taking a long time to execute a callback (Event Loop) or a task (Worker), we call it "blocked". While a thread is blocked working on behalf of one client, it cannot handle requests from any other clients. 

 This provides two motivations for blocking neither the Event Loop nor the Worker Pool:

 Performance: If you regularly perform heavyweight activity on either type of thread, the throughput (requests/second) of your server will suffer.

Security: If it is possible that for certain input one of your threads might block, a malicious client could submit this "evil input", make your threads block, and keep them from working on other clients. This would be a Denial of Service attack.

## A quick review of Node

Node uses the Event-Driven Architecture: it has an Event Loop for orchestration and a Worker Pool for expensive tasks.

### What code runs on the Event Loop?
When they begin, Node applications first complete an initialization phase, require'ing modules and registering callbacks for events. 
Node applications then enter the Event Loop, responding to incoming client requests by executing the appropriate callback.
This callback executes synchronously, and may register asynchronous requests to continue processing after it completes.  The callbacks for these asynchronous requests will also be executed on the Event Loop.

The Event Loop will also fulfill the non-blocking asynchronous requests made by its callbacks, e.g., network I/O.

In summary, the Event Loop executes the JavaScript callbacks registered for events, and is also responsible for fulfilling non-blocking asynchronous requests like network I/O.

### What code runs on the Worker Pool?

Node's Worker Pool is implemented in libuv (docs), which exposes a general task submission API.

Node uses the Worker Pool to handle "expensive" tasks. This includes I/O for which an operating system does not provide a non-blocking version, as well as particularly CPU-intensive tasks.

These are the Node module APIs that make use of this Worker Pool:
1. I/O-intensive
    1. DNS: dns.lookup(), dns.lookupService().
    2. File System: All file system APIs except fs.FSWatcher() and those that are explicitly synchronous use libuv's threadpool.

2. CPU-intensive
    1. Crypto: crypto.pbkdf2(), crypto.randomBytes(), crypto.randomFill().
    2. Zlib: All zlib APIs except those that are explicitly synchronous use libuv's threadpool.


For the sake of completeness, we note that when you call one of these APIs from a callback on the Event Loop, the Event Loop pays some minor setup costs as it enters the Node C++ bindings for that API and submits a task to the Worker Pool.

These costs are negligible compared to the overall cost of the task, which is why the Event Loop is offloading it. When submitting one of these tasks to the Worker Pool, Node provides a pointer to the corresponding C++ function in the Node C++ bindings.

### How does Node decide what code to run next?
Abstractly, the Event Loop and the Worker Pool maintain queues for pending events and pending tasks, respectively.

In truth, the Event Loop does not actually maintain a queue. Instead, it has a collection of file descriptors that it asks the operating system to monitor.
These file descriptors correspond to network sockets, any files it is watching, and so on.

In contrast, the Worker Pool uses a real queue whose entries are tasks to be processed. A Worker pops a task from this queue and works on it, and when finished the Worker raises an "At least one task is finished" event for the Event Loop.

### What does this mean for application design?

In a one-thread-per-client system like Apache, each pending client is assigned its own thread. If a thread handling one client blocks, the operating system will interrupt it and give another client a turn. The operating system thus ensures that clients that require a small amount of work are not penalized by clients that require more work.

Because Node handles many clients with few threads, if a thread blocks handling one client's request, then pending client requests may not get a turn until the thread finishes its callback or task. 
The fair treatment of clients is thus the responsibility of your application. This means that you shouldn't do too much work for any client in any single callback or task.

### Don't block the Event Loop
The Event Loop notices each new client connection and orchestrates the generation of a response. All incoming requests and outgoing responses pass through the Event Loop. This means that if the Event Loop spends too long at any point, all current and new clients will not get a turn.
You should make sure you never block the Event Loop. In other words, each of your JavaScript callbacks should complete quickly. This of course also applies to your await's, your Promise.then's, and so on.


### Blocking the Event Loop: REDOS

One common way to block the Event Loop disastrously is by using a "vulnerable" regular expression.

#### Avoiding vulnerable regular expressions
A vulnerable regular expression is one on which your regular expression engine might take exponential time, exposing you to REDOS on "evil input"

#### Blocking the Event Loop: Node core modules
Several Node core modules have synchronous expensive APIs, including:

Encryption
Compression
File system
Child process

### Blocking the Event Loop: JSON DOS
JSON.parse and JSON.stringify are other potentially expensive operations. While these are O(n) in the length of the input, for large n they can take surprisingly long.


### Complex calculations without blocking the Event Loop

Suppose you want to do complex calculations in JavaScript without blocking the Event Loop. You have two options: partitioning or offloading.

============================================================
1) How can you avoid callback hells?
    modularization: break callbacks into independent functions
    use generators with Promises
    use async/await


2) What are Promises?
    Promises are a concurrency primitive.  Promises can help you better handle async operations.

3)How can you make sure your dependencies are safe?
When writing Node.js applications, ending up with hundreds or even thousands of dependencies can easily happen.
For example, if you depend on Express, you depend on 27 other modules directly, and of course on those dependencies' as well, so manually checking all of them is not an option!

The only option is to automate the update / security audit of your dependencies. For that there are free and paid options:

npm outdated
Trace by RisingStack
NSP
GreenKeeper
Snyk


4) What's wrong with the code snippet?
new Promise((resolve, reject) => {
  throw new Error('error')
}).then(console.log)

As there is no catch after the then. This way the error will be a silent one, there will be no indication of an error thrown.
(node:42271) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 2): Error: error


Add catch in Prmoise to solve this.

//Current way of handling all errors. This will be deprecated.
process.on('unhandledRejection', (err) => {
  console.log(err)
})
(node:42271) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.

======================
https://blog.risingstack.com/node-js-interview-questions/
1) What's the event loop?
Node.js runs using a single thread, at least from a Node.js developer's point of view. Under the hood Node.js uses many threads through libuv.

Every I/O requires a callback - once they are done they are pushed onto the event loop for execution. If you need a more detailed explanation, I suggest viewing this video:

2)Why npm shrinkwrap is useful?
This command locks down the versions of a package's dependencies so that you can control exactly which versions of each dependency will be used when your package is installed. - npmjs.com

It is useful when you are deploying your Node.js applications - with it you can be sure which versions of your dependencies are going to be deployed.

3) It uses asynchronous, event-driven I/O instead of separate processes or threads

4) What are Globals in Node.js?
    Three keywords in Node.js constitute as Globals. These are:

    Global – it represents the Global namespace object and acts as a container for all other             objects.
    Process – It is one of the global objects but can turn a synchronous function into an async          callback. It can be accessed from anywhere in the code and it primarily gives back          information about the application or the environment. 
    Buffer – it is a class in Node.js to handle binary data. 


5) My notes
   Each piece of JavaScript code is also always run to completion. There is no concept of temporarily suspending the execution of some JavaScript code to switch over to another piece of JavaScript code. This would be called preemptive multithreading. It is the way processes and threads work at the operating system level, but not in Node.js. This is the crucial missing link that renders the example broken.

    To allow other parts of the program to proceed, you have to deliberately give control back. You have to let others use the only available thread. You have to play together with the other parts of the program.

    ### Need to return control back to the system
    For the single-threaded model to work a program needs to return back control to "the system". In Node.js this system is called the event loop and similar concept is present in browsers as well.


################
    Node.js is an asynchronous event driven framework.

    Node.js is closely related to events. As the definition suggests it is inherently driven by them.
    
    The event loop is a huge while loop that is started in the node binary. It repeatedly takes incoming events and fires any event listeners subscribed to those events one at a time.

    You need to let control back to this event loop. You can do it in several ways. You let control back to the system

    by letting your top level JavaScript code reach its end
    by letting your event listener JavaScript code reach its end
    by using yield (ES6)
    by using await (ES7)
    And you need to do this frequently. The larger the amount of concurrency in your program the more important it is to frequently let other parts of the program to advance as well.

