A closure is the combination of a function and the lexical environment within which that function was declared. 

functions in JavaScript form closures. This environment consists of any local variables that were in-scope at the time the closure was created. 


        function makeAdder(x) {
            return function(y) {
                return x + y;
            };
        }

        var add5 = makeAdder(5);
        var add10 = makeAdder(10);

        console.log(add5(2));  // 7
        console.log(add10(2)); // 12

## Usage:
Closures are useful because they let you associate some data (the lexical environment) with a function that operates on that data. (parallel to Object oriented programming)

### Usage: 
front-end JavaScript is event-based. we define some behavior, then attach it to an event that is triggered by the user (such as a click or a keypress).

        function makeSizer(size) {
            return function() {
                document.body.style.fontSize = size + 'px';
            };
        }

        var size12 = makeSizer(12);
        var size14 = makeSizer(14);
        var size16 = makeSizer(16);

        document.getElementById('size-12').onclick = size12;

Private methods:
        var counter = (function() {
            var privateCounter = 0;
            function changeBy(val) {
                privateCounter += val;
            }

            return {
                increment: function() {
                    changeBy(1);
                },
                decrement: function() {
                    changeBy(-1);
                },
                value: function() {
                    return privateCounter;
                }
            };
        }
        )();

        console.log(counter.value()); // logs 0
        counter.increment();
        counter.increment();
        console.log(counter.value()); // logs 2
        counter.decrement();
        console.log(counter.value()); // logs 1

        //Showing encapsualtion & datahiding

        var makeCounter = function() {
        var privateCounter = 0;
        function changeBy(val) {
            privateCounter += val;
        }
        return {
            increment: function() {
            changeBy(1);
            },
            decrement: function() {
            changeBy(-1);
            },
            value: function() {
            return privateCounter;
            }
        }  
        };

        var counter1 = makeCounter();
        var counter2 = makeCounter();
        alert(counter1.value()); /* Alerts 0 */
        counter1.increment();
        counter1.increment();
        alert(counter1.value()); /* Alerts 2 */
        counter1.decrement();
        alert(counter1.value()); /* Alerts 1 */
        alert(counter2.value()); /* Alerts 0 */


        Each closure references a different version of the privateCounter variable through its own closure. Each time one of the counters is called, its lexical environment changes by changing the value of this variable; however changes to the variable value in one closure do not affect the value in the other closure.

        Using closures in this way provides a number of benefits that are normally associated with object-oriented programming -- in particular, data hiding and encapsulation.


### Interesting problems
        function showHelp(help) {
            document.getElementById('help').innerHTML = help;
        }

        function setupHelp() {
        var helpText = [
            {'id': 'email', 'help': 'Your e-mail address'},
            {'id': 'name', 'help': 'Your full name'},
            {'id': 'age', 'help': 'Your age (you must be over 16)'}
            ];

        for (var i = 0; i < helpText.length; i++) {
            var item = helpText[i];
            document.getElementById(item.id).onfocus = function() {
                showHelp(item.help);
            }
        }
        }

        setupHelp();

    Note: showHelp will always show "Your age (you must be over 16)", which is last entry of for-loop.
    Reason:
    The reason for this is that the functions assigned to onfocus are closures; they consist of the function definition and the captured environment from the setupHelp function's scope.
    Three closures have been created by the loop, but each one shares the same single lexical environment, which has a variable with changing values (item.help).
     The value of item.help is determined when the onfocus callbacks are executed. Because the loop has already run its course by that time, the item variable object (shared by all three closures) has been left pointing to the last entry in the helpText list.


    #### One solution in this case is to use more closures: in particular, to use a function factory as described earlier:

    function makeHelpCallback(help) {
        return function() {
            showHelp(help);
        };
    }

    ...
    document.getElementById(item.id).onfocus = makeHelpCallback(item.help);

    #### Another solution: Anonymous callback

    document.getElementById(item.id).onfocus = function() {
         showHelp(item.help);
    }

    #### Use let - block scoped variable
    This example uses let instead of var, so every closure binds the block-scoped variable, meaning that no additional closures are required.

