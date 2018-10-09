# Generators

Generators are functions which can be exited and later re-entered. Their context (variable bindings) will be saved across re-entrances.

This pattern is what async functions are built on top of.

Calling a generator function does not execute its body immediately; an iterator object for the function is returned instead.

When the iterator's next() method is called, the generator function's body is executed until the first yield expression which specifies the value to be returned from the iterator or, with yield*, delegates to another generator function.

The next() method returns an object with a value property containing the yielded value and a done property which indicates whether the generator has yielded its last value as a boolean.

        function* generator(i) {
        yield i;
        yield i + 10;
        }

        var gen = generator(10);

        var obj1 = gen.next();
        console.log(obj1.value);
        console.log(obj1.done);
        // expected output: 10, false

        var obj2 = gen.next();
        console.log(obj2.value);
        console.log(obj2.done);
        // expected output: 20, false

        var obj3 = gen.next();
        console.log(gen.next().done);
        console.log(gen.next().value);
        //expected output: true, undefined


???????
Calling the next() method with an argument will resume the generator function execution, replacing the yield expression where execution was paused with the argument from next(). 


A return statement in a generator, when executed, will make the generator finished (i.e the done property of the object returned by it will be set to true). If a value is returned, it will be set as the value property of the object returned by the generator.
Much like a return statement, an error thrown inside the generator will make the generator finished -- unless caught within the generator's body.
When a generator is finished, subsequent next calls will not execute any  of that generator's code, they will just return an object of this form: {value: undefined, done: true}.


        function* generator(i) {
        if (1==1) {
            return 777;
            }
        yield i;
        yield i + 10;
        }

        var gen = generator(10);

        var obj1 = gen.next();
        console.log(obj1.value);
        console.log(obj1.done);
        // expected output: 777, true //Value of iterator is the return value and done is set on generator

        gen.next().value;//Is undefined. As the return statement has finished the generator

======================================

Example 1:
        function* idMaker() {
            let index = 0;
            while(true) {
                yield index++;
            }
        }

        const sequence = idMaker();
        console.log(sequence.next().value);
        console.log(sequence.next().value);
        console.log(sequence.next().value);

Example 2:
        function* anotherGenerator(i) {
            yield i + 1;
            yield i + 2;
            yield i + 3;
        }

        function* generator(i) {
            yield i;
            yield* anotherGenerator(i);
            yield i+4;
        }

        const gen = generator(10);
        console.log(gen.next().value); // 10
        console.log(gen.next().value); // 11
        console.log(gen.next().value); // 12
        console.log(gen.next().value); // 13
        console.log(gen.next().value); // 20


## Passing arguments into Generators

        function* logGenerator() {
            console.log(0);
            console.log(1, yield);
            console.log(2, yield);
            console.log(3, yield);
        }

        var gen = logGenerator();

        // the first call of next executes from the start of the function
        // until the first yield statement
        gen.next();             // 0
        gen.next('pretzel');    // 1 pretzel
        gen.next('california'); // 2 california
        gen.next('mayonnaise'); // 3 mayonnaise

## Return statement in a generator
        function* yieldAndReturn() {
            yield "Y";
            return "R";
            yield "unreachable";
        }

        var gen = yieldAndReturn()
        console.log(gen.next()); // { value: "Y", done: false }
        console.log(gen.next()); // { value: "R", done: true }
        console.log(gen.next()); // { value: undefined, done: true }

## Generators are not constructable
        function* f() {}
        var obj = new f; // throws "TypeError: f is not a constructor