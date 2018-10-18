

1. for statement
    for (var i = 0; i < 5; i++) {

    }
    
    
 2. for...in statement
 
    for (var i in obj) {
      result += obj_name + '.' + i + ' = ' + obj[i] + '<br>';
    }
 
 The for...in statement iterates a specified variable over all the enumerable properties of an object.
 
 
 3.  for...of statement
 
     for (variable of object) {
      statement
    }
    
    The for...of statement creates a loop Iterating over iterable objects (including Array, Map, Set, arguments object and so on),
    invoking a custom iteration hook with statements to be executed for the value of each distinct property.
    
    While for...in iterates over property names, for...of iterates over property values:


      var arr = [3, 5, 7];
      arr.foo = 'hello';

      for (var i in arr) {
         console.log(i); // logs "0", "1", "2", "foo"
      }

      for (var i of arr) {
         console.log(i); // logs 3, 5, 7
      }
      
      
      
    Loops in ES5 & ES6:
    myArray.forEach( (value) => {
       console.log(value);
    }); //No break here or return. Since handler for each element is callback function. That function is returned.
    //If u add break, then error since not a context for break
    
    
    //For SET
    for (var word of uniqueWords) {
      console.log(word);
    }
    
    //For MAP
    for (var [key, value] of phoneBookMap) {
      console.log(key + "'s phone number is: " + value);
    }

    
