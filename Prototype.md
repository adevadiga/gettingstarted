# Prototype

class keyword is introduced in ES2015, but its syntactical sugar.
Javascript remains prototype based.

When it comes to inheritence, JavaScript has only one construct: objects.

Each object has a private property which holds a link to another object called its prototype.
That prototype object has a prototype of its own, and so on until an object is reached with null as its prototype.

Nearly all objects in JavaScript are instances of Object which sits on the top of a prototype chain.
const foo = {};
> foo.__proto__ = some object
> foo.__proto__.__proto__ = is NULL


## Inheritance with the prototype chain

JavaScript objects have a link to a prototype object. 
When trying to access a property of an object, the property will not only be sought on the object but on the prototype of the object, the prototype of the prototype, and so on until either a property with a matching name is found or the end of the prototype chain is reached.

    let f = function () {
        this.a = 1;
        this.b = 2;
    }
    let o = new f();

    f.prototype.b = 3;
    f.prototype.c = 4;

    console.log(o.a); // 1
    console.log(o.b); // 2
    console.log(o.c); // 4
    console.log(o.d); // undefined

## Inheriting "methods"

JavaScript does not have "methods" in the form that class-based languages define them.
In JavaScript, any function can be added to an object in the form of a property.

When an inherited function is executed, the value of this points to the inheriting object, not to the prototype object where the function is an own property.

        var o = {
            a: 2,
            m: function() {
                return this.a + 1;
            }
        };

        console.log(o.m()); // 3
        // When calling o.m in this case, 'this' refers to o

        var p = Object.create(o);
        // p is an object that inherits from o

        p.a = 4; // creates a property 'a' on p
        console.log(p.m()); // 5


> Note: var foo = {} 
        foo.__proto__

        var foo = function(){}
        foo.prototype


## Different ways of creating objects and the prototyp chain

### Objects created with syntax constructs
        var o = {a: 1};
        o ---> Object.prototype ---> null

        var b = ['yo', 'whadup', '?'];
        b ---> Array.prototype ---> Object.prototype ---> null

        function f() {
            return 2;
        }
        f ---> Function.prototype ---> Object.prototype ---> null

### With a constructor
A "constructor" in JavaScript is "just" a function that happens to be called with the new operator.

        function Graph() {
            this.vertices = [];
            this.edges = [];
        }

        Graph.prototype = {
            addVertex: function(v) {
                this.vertices.push(v);
            }
        };

        var g = new Graph();
        // g is an object with own properties 'vertices' and 'edges'.
        // g.[[Prototype]] is the value of Graph.prototype when new Graph() is executed.


### With a Object.create
        var a = {a: 1}; 
        // a ---> Object.prototype ---> null

        var b = Object.create(a);
        // b ---> a ---> Object.prototype ---> null
        console.log(b.a); // 1 (inherited)

        var c = Object.create(b);
        // c ---> b ---> a ---> Object.prototype ---> null

        var d = Object.create(null);
        // d ---> null
        console.log(d.hasOwnProperty); 
        // undefined, because d doesn't inherit from Object.prototype


### With the class keyword

class Polygon {

    constructor(height, width) {
        this.height = height;
        this.weight = weight;
    }
}

class Square extends Polygon {

    constructor(sideLength) {
        super(sideLength, sideLength);
    }

    get area() {
        return this.height * this.width;
    }

    set sideLength(newLength) {
        this.height = newLength;
        this.width = newLength;
    }
}

var square = new Square(2);


## Performance
The lookup time for properties that are high up on the prototype chain can have a negative impact on the performance, and this may be significant in the code where performance is critical.
Additionally, trying to access nonexistent properties will always traverse the full prototype chain.

 To check whether an object has a property defined on itself and not somewhere on its prototype chain, it is necessary to use the hasOwnProperty method which all objects inherit from Object.prototype.






 ###################
 # Summary
Almost all objects in JavaScript have the prototype property. By using it and more specifically the prototype chain we can mimic inheritance. 

The prototype is a reference to another object and it is used whenever JS can’t find the property you’re looking for on the current object.

Simply put, whenever you call a property on an object and it doesn’t exist, JavaScript will go to the prototype object and look for it there. If it finds it it will use it, if not it will go to that object’s property and look there. This can bubble up all the way to Object.prototype before returning undefined.


#######
My Note: prototype of any object is of type object. As it can refer any other object , it can also have its own property:

function Dog(name) {
    this.name = name;
}

Dog.prototype => Object

Dog.fn1 = function() {} => Not added to prototype
Dog.fn1 is just functiona with name Dog.fn1

Dog.prototype.fn1 = function() {} => added to prototype
Dog.prototype  =>   fn1: ƒ ()
                    constructor: ƒ Dog(name)
                    __proto__: Object


var tommy = new Dog()
tommy.prototype // Doesn't exist. However tommy is of Type Dog. Whose prototype is Object



####
function Animal(name) {
    this.name = name;
}

Animal.prototype.sleep = function() {
    console.log(this.name + ': Zzz...');
}

function Dog(name) {
    this.name = name;
}

// Create a reference for the prototype
Dog.prototype = Object.create(new Animal());

Dog.prototype.makeSound = function() {
    console.log(this.name + ': Woof woof!');
}

Dog.prototype.sleep = function() {
    console.log(this.name + ': Overriding Zzzz....');
}

var dog = new Dog('Lassie');
dog.makeSound(); // Lassie: Woof woof!
dog.sleep(); // Lassie: Overriding Zzzz....


var human = new Animal("Chinni");
human.sleep();


https://www.digitalocean.com/community/tutorials/understanding-prototypes-and-inheritance-in-javascript



#### Prototype Inheritance
When you attempt to access a property or method of an object, JavaScript will first search on the object itself, and if it is not found, it will search the object's [[Prototype]]. If after consulting both the object and its [[Prototype]] still no match is found, JavaScript will check the prototype of the linked object, and continue searching until the end of the prototype chain is reached.

At the end of the prototype chain is Object.prototype. All objects inherit the properties and methods of Object. Any attempt to search beyond the end of the chain results in null.


--------------
# Constructor Functions
Constructor functions are functions that are used to construct new objects. The new operator is used to create new instances based off a constructor function. We have seen some built-in JavaScript constructors, such as new Array() and new Date(), but we can also create our own custom templates from which to build new objects.


# Example of call and linking prototypes
        // Initialize constructor functions
        function Hero(name, level) {
        this.name = name;
        this.level = level;
        }

        function Warrior(name, level, weapon) {
        Hero.call(this, name, level);

        this.weapon = weapon;
        }

        function Healer(name, level, spell) {
        Hero.call(this, name, level);

        this.spell = spell;
        }

        // Link prototypes and add prototype methods
        Warrior.prototype = Object.create(Hero.prototype);
        Healer.prototype = Object.create(Hero.prototype);

        Hero.prototype.greet = function () {
        return `${this.name} says hello.`;
        }

        Warrior.prototype.attack = function () {
        return `${this.name} attacks with the ${this.weapon}.`;
        }

        Healer.prototype.heal = function () {
        return `${this.name} casts ${this.spell}.`;
        }

        // Initialize individual character instances
        const hero1 = new Warrior('Bjorn', 1, 'axe');
        const hero2 = new Healer('Kanin', 1, 'cure');