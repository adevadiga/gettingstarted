# Function.prototype.call()

You use call or apply when you want to pass a different this value to the function. In essence, this means that you want to execute a function as if it were a method of a particular object. The only difference between the two is that call expects parameters separated by commas, while apply expects parameters in an array.


Unlike other languages - in JavaScript this does not refer to the current object - rather to the execution context and can be set by the caller.



================================

The call() method calls a function with a given this value and arguments provided individually.

The call() allows for a function/method belonging to one object to be assigned and called for a different object.

call() provides a new value of this to the function/method. With call, you can write a method once and then inherit it in another object, without having to rewrite the method for the new object.

        function Product(name, price) {
            this.name = name;
            this.price = price;
        }

        function Food(name, price) {
            Product.call(this, name, price);
            this.category = 'food';
        }

        function Toy(name, price) {
            Product.call(this, name, price);
            this.category = 'toy';
        }

        var cheese = new Food('feta', 5);
        var fun = new Toy('robot', 40);


## Using call to invoke an anonymous function
        var animals = [
            { species: 'Lion', name: 'King' },
            { species: 'Whale', name: 'Fail' }
        ];

        for (var i = 0; i < animals.length; i++) {
            (function(i) {
                this.print = function() {
                console.log('#' + i + ' ' + this.species
                            + ': ' + this.name);
                }
                this.print();
            }).call(animals[i], i);
        }

## Using call to invoke a function and specifying the context for 'this'

        function greet() {
            var reply = [this.animal, 'typically sleep between', this.sleepDuration].join(' ');
            console.log(reply);
        }

        var obj = {
            animal: 'cats', sleepDuration: '12 and 16 hours'
        };

        greet.call(obj);  // cats typically sleep between 12 and 16 hours


## Using call to invoke a function and without specifying the first argument
In the example below, we invoke the display function without passing the first argument. If the first argument is not passed, the value of this is bound to the global object.

    var sData = 'Wisen';
            
    function display() {
        console.log('sData value is %s ', this.sData);
    }

    display.call();  // sData value is Wisen 

    The value of this will be undefined in strict mode...



#   Function.prototype.apply()

The apply() method calls a function with a given this value, and arguments provided as an array.

> Note: While the syntax of this function is almost identical to that of call(), the fundamental difference is that call() accepts an argument list, while apply() accepts a single array of arguments.
