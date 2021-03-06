#### Module 4 - Generators   Introduction to Generators   Intro to Generator Functions Video

# Module 4 Intro

---

#### Module 4 - Generators   Introduction to Generators   Introduction to Generator Functions

# Introduction to Generators

**What are Generators?**

Generators are functions that can be paused and resumed. Generators can send out values when pausing 
and take in values when resuming.

**Why are Generators important?**

Generators are important because they allow asynchronous functions to written like normal synchronous functions.

**What are we going to learn?**

* How to create and use generators
* How to send values in and out of generators
* How to use Generators with asynchronous functions

---

#### Module 4 - Generators   Generator Functions and Generator Objects   Creating a Generator Function

# Creating a Generator Function

Sample code of a Generator function:

```javascript
function* genFunc() {
    yield 'a';
    yield;
    yield 123;
        
    return "finished";
}
```

### Function* Keyword

Generator functions look similar to regular functions, except that they have an asterisk (`*`) after the 
function keyword. This syntax may look similar to the pointer notation from other languages, but it is unrelated.

Notice how the `function*` keyword is used the declare a Generator function:

```javascript
function* genFunc() { //notice the function* keyword
       
}
```

### Yield Keyword

The `yield` keyword is used to pause the generator. The `yield` keyword may also be used to receive input and 
send output from the generator.

Notice how the `yield` keyword is used to pause and send several different types of output from the Generator 
function:

```javascript
    yield 'a'; //pauses the generator and sends out a value of 'a'
    yield;     //pauses the generator and sends out an undefined value
    yield 123; //pauses the generator and sends out a value of 123
```

### Return Value

Generator Functions have an optional `return` value. Omitting the return value is equivalent to returning 
an undefined value. The return value of Generator functions is often left unused.

Notice the `return` value of the Generator function:

```javascript
return "finished"; //return value of "finished"
```

---

#### Module 4 - Generators   Generator Functions and Generator Objects   Creating and Iterating through a Generator Object

# Creating and Iterating through a Generator Object

### Creating a Generator Object

A **Generator Object** is returned from calling a **Generator function**. It is important to not confuse 
Generator Objects with Generator functions. 

Notice how a **Generator Object** is created by calling a Generator function:

```javascript
function* genFunc() {
    console.log("started");
    yield 'a';
    console.log("passed first yield");
    yield;
    console.log("passed second yield");
    yield 123;
    console.log("passed third yield");
        
    return "finished";
}

var genObject = genFunc(); //creates a generator object called genObject
```

### Iterating through a Generator Object with next()

Generator Objects conform to the iterator protocol and may be iterated with the `next()` method.

Generator functions are initially paused and the first call to `next()` starts the Generator function. The 
Generator function then runs until it hits the first `yield` keyword and then pauses. Subsequent calls 
to `next()`  will resume the Generator function until the next `yield` keyword appears.

The `next()` method returns an object with two properties:

* `done` - a boolean indicating whether the Generator function has processed all of the `yield` statements or has already returned. 
* `value` - the value associated with the most recent yield statement.

Notice how the `next()` method is used to iterate through all of the yield statements:


```javascript
var a = genObject.next(); // Object {value: 'a', done: false}
//console.log("started");

var b = genObject.next(); // Object {value: undefined, done: false}
//console.log("passed first yield"); 

var c = genObject.next(); // Object {value: 123, done: false}
//console.log("passed second yield");
```

After all of the `yield` statements have been processed with `next()`, the following `next()` call returns an 
object with a `value` property equal to the Generator function return value and a `done` property set to true. 
If the `return` statement was omitted from the Generator function then the value property will be undefined. 
After the `done` property is `true` in one of the returned objects, additional `next()` calls will return 
objects with an `undefined` `value` property and a `true` `done` property. `Yield` statements after the `return` 
statement are ignored.

Notice how additional calls to `next()` behave:

```javascript
var d = genObject.next(); // Object {value: "finished", done: true} <-- value property takes the return value of genFunc()
//console.log("passed third yield");

var e = genObject.next(); // Object {value: undefined, done: true} <-- additional next() calls return this
```

---

#### Module 4 - Generators   Generator Functions and Generator Objects   Throwing Errors Inside a Generator Function

# Throwing Errors Inside a Generator Function

If an error is encountered within a Generator function, then the error will be thrown by the next() call that encounters the error. The next() call that throws the error will return an undefined value and additional yield statements after the error are ignored. Additional next() calls after the error will also return undefined values.

Notice the affects of throwing an error within a Generation function:

```javascript
function* genFunc() {

    yield 'a';
    yield 'b';
    throw new Error("error thrown by genFunc()!");
    yield 'c';
    yield 'd';
        
}

var genObject = genFunc();

try{
    var a = genObject.next(); // Object {value: 'a', done: false}
    var b = genObject.next(); // Object {value: 'b', done: false}
    var c = genObject.next(); // undefined <-- since an uncaught error was thrown, the generator function terminated
    //console.log("error thrown by genFunc()!") occurs
    var d = genObject.next(); // undefined <-- other yield statements are ignored after the error
}
catch(e){
  console.log(e.message);
}
```

---

#### Module 4 - Generators   Generator Functions and Generator Objects   Yielding to other Generators

# Yielding to other Generators

Yield* Keyword

The `yield*` keyword is used to call another Generator function within a Generator function.

Notice how the yield* statement is used to call genFuncA() within genFuncB():

```javascript
function* genFuncA() {
    yield 'a';
    yield 'b';
    yield 'c';

    return "done with genFuncA()!"
        
}

function* genFuncB(){
    yield 1;
    yield* genFuncA(); // contains iterable [a,b,c]
    yield 2;
    yield 3;

    return "done with genFuncB()!";
}

var genObject = genFuncB();

var a = genObject.next(); //Object {value: 1, done: false}
var b = genObject.next(); //Object {value: 'a', done: false}
var c = genObject.next(); //Object {value: 'b', done: false}
var d = genObject.next(); //Object {value: 'c', done: false}
var e = genObject.next(); //Object {value: 2, done: false}
var f = genObject.next(); //Object {value: 3, done: false}
var g = genObject.next(); //Object {value: "done with genFuncB()!", done: true}
```

The `yield*` statement does not add the return value of the generator function that it calls to its 
list of iterables. Instead, the return value may be accessed by the return value of the `yield*` statement.

Notice how the `yield* genFuncA()` statement returns the return value of `genFuncA()`:


```javascript
function* genFuncA() {
    yield 'a';
    yield 'b';

    return "done with genFuncA()!"
        
}

function* genFuncB(){
    yield 1;
    var returnVal = yield* genFuncA(); // contains iterable list [a,b] and returns with value "done with genFuncA()!"
    yield returnVal; // returnVal is equal to"done with genFuncA()
    yield 2;

    return "done with genFuncB()!";
}

var genObject = genFuncB();

var a = genObject.next(); //Object {value: 1, done: false}
var b = genObject.next(); //Object {value: 'a', done: false}
var c = genObject.next(); //Object {value: 'b', done: false}
var d = genObject.next(); //Object {value: "done with genFuncA()!", done: false}
var e = genObject.next(); //Object {value: 2, done: false}
var f = genObject.next(); //Object {value: "done with genFuncB()!", done: true}
```

The `yield*` statement can be used on any iterable in addition to Generator functions.

Notice how the `yield*` statement is used to `yield` all of the values of in an array:


```javascript
function* genFunc(){
    yield 1;
    yield* [2,3,4]; //the array [2,3,4] is iterable
    yield 5;

}

var genObject = genFunc();

var a = genObject.next(); //Object {value: 1, done: false}
var b = genObject.next(); //Object {value: 2, done: false}
var c = genObject.next(); //Object {value: 3, done: false}
var d = genObject.next(); //Object {value: 4, done: false}
var e = genObject.next(); //Object {value: 5, done: false}
var f = genObject.next(); //Object {value: undefined, done: true}
```

---

#### Module 4 - Generators   Generator Functions and Generator Objects   Code Demo
 
# Demo Video: Creating and Iterating through Generators

https://youtu.be/1IVOjTl7ZlU

---

#### Module 4 - Generators   More on Generator Objects   Sending Input to Generator Functions

# Sending Input to Generator Functions

**External resource:** https://gist.github.com/ericelliott/890c20d18bcc4362048dba2dca8e67ac

In addition to iterating through Generator Objects, `next()` can also be used to send values back into Generator functions. This is 
accomplished by passing a value into the `next()` method call as an argument. The value that is passed into the `next()` method call 
eventually becomes the return value of the most recent yield statement. Since the first `next()` call starts the Generator function, 
any value that gets passed into it will be ignored.

Notice how the `next()` method call is used to send values back into the Generator function:

```javascript
function* genFunc(){
    var a = yield;
    console.log(a); //a = 1
    var b = yield;  
    console.log(b); //b = 2
    var c = yield;
    console.log(c); //c = 3

}

var genObject = genFunc();

genObject.next(0); //starts genFunc(), the value inside the next() call is ignored
genObject.next(1); //sends a value of 1 to genFunc()
genObject.next(2); //sends a value of 2 to genFunc()
genObject.next(3); //sends a value of 3 to genFunc()
genObject.next(4); //the value inside next() is ignored because genFunc() has no more yields
```

The `next()` method can also be used to modify the values sent by the yield statement and send them back.

Notice how the `next()` method is used to obtain values from yield, modify them, and then send them back:

```javascript
function* genFunc(){
    var a = yield 'a';
    console.log(a); // a = 'a!'
    var b = yield 'b';  
    console.log(b); // b = 'B'
    var c = yield 'c';
    console.log(c); // c = 'abc'

}

var genObject = genFunc();

var w = genObject.next(); //starts genFunc(), w = Object {value: 'a', done: false}
var x = genObject.next(w.value + '!'); //sends a value of "a!" to genFunc(), x = Object {value: 'b', done: false}
var y = genObject.next(x.value.toUpperCase()); //sends a value of 'B' to genFunc(), y = Object {value: 'c', done: false}
var z = genObject.next(w.value + x.value + y.value); //sends a value of 'abc' to genFunc(), z = Object {value: 'undefined', done: true}
```

---

#### Module 4 - Generators   More on Generator Objects   Other Methods to Iterate

# Other Methods to Iterate through Generator Objects

### For...Of

Notice how the `For...Of` statement is used to iterate through a Generator Object:

```javascript
function* genFunc(){
    yield 'a';
    yield;  
    yield* [1,2,3];
    yield 123;

    return "finished";

}

for (var x of genFunc()){ //for...of statement
    console.log(x); 
}
//Outputs:
//'a'
// undefined
// 1
// 2
// 3
// 123
// <-- return value is not outputted
```

### Spread Operator (...)

Notice how the **spread operator** is used to iterate through a Generator object:

```javascript
function* genFunc(){
    yield 'a';
    yield;  
    yield* [1,2,3];
    yield 123;

    return "finished";

}
var arr = [...genFunc()]; //...spread operator
// arr = ['a',undefined,1,2,3,123]
```

### Destructuring

Notice how the **destructuring assignment** is used to iterate through a Generator object:

```javascript
function* genFunc(){
    yield 'a';
    yield;  
    yield* [1,2,3];
    yield 123;

    return "finished";

}

var [a,b,c,d,e,f,g] = genFunc(); //destructuring assignment
// a = 'a'
// b = undefined
// c = 1
// d = 2
// e = 3
// f = 123
// g = undefined <-- g is undefined because there are no more yields
```

---

#### Module 4 - Generators   More on Generator Objects   Return()

# Return()

Generator Objects have a return() method that terminates the Generator function. Return() causes a return statement to be performed at the most recent yield statement. The return() method takes in one optional variable that is used as the return value of the Generator function. Calling return(x) will return an object with a value property equal to x and a done property of true. After return() is called, subsequent yield statements in the Generator function are ignored. 

Notice how calling return() affects the generator function:

```javascript
function* genFunc(){
    yield 'a';
    yield 'b'
    yield 'c'
    return "finished";

}

var genObject = genFunc();

var a = genObject.next(); // a = Object {value: 'a', done: false}
var b = genObject.return('return() was called'); // b = Object {value: "return() was called", done: true}
var c = genObject.next(); // c = Object {value: undefined, done: true}
```

---

#### Module 4 - Generators   More on Generator Objects   Throwing Errors

# Throwing Errors

### Throw()

Generator Objects have a throw() method that causes an error to be thrown at the most recent yield statement. The throw() method takes in one argument, which is commonly an Error object.

Notice how throw() affects the Generator function:

```javascript
function* genFunc(){

        var a = yield 'a';
        console.log(a); // a = 123
        var b = yield 'b'; //exception is thrown, function exits
        //the code below never occurs because an exception occurred and was uncaught
        console.log(b); 
        var c = yield 'c'; 
        console.log(c); 

        return "finished!"; 

}

var genObject = genFunc();

var w = genObject.next(); // w = Object {value: 'a', done: false}, starts generator function
var x = genObject.next(123); // x = Object {value: 'b', done: false}
var y = genObject.throw(new Error("error thrown!")); // thrown() is called, y = undefined
var z = genObject.next('abc'); // z = undefined
```

---

#### Module 4 - Generators   More on Generator Objects   Code Demo

# Code Demo: Sending Input into Generators

https://youtu.be/Vp0-U16ZOvc

https://youtu.be/ZW6Kb-gu9Vk

---

#### Module 4 - Generators   Using Generators with Asynchronous Functions   Using Generators with Asynchronous Functions

# Using Generators with Asynchronous Functions

**Generator functions** work well with asynchronous functions that return 
`Promises`. This is because **Generator functions** can yield a `Promise`, 
process the Promise result asynchronously, and then receive the Promise 
result back. This allows asynchronous code to be written inside 
generator functions like normal synchronous functions. 

Notice how Promises can be written in a synchronous way inside 
Generator functions:

```javascript
function* genFunc(){ //looks synchronously written

        var post1title = yield fetch("https://jsonplaceholder.typicode.com/posts/1");
        console.log(post1title); 
        //post1title = "sunt aut facere repellat provident occaecati excepturi optio reprehenderit"
        var post2title = yield fetch("https://jsonplaceholder.typicode.com/posts/2");
        console.log(post2title);
        //post2title = "qui est esse"


}

var genObject = genFunc(); //creating generator object

var yieldedObject = genObject.next(); //starting generator and returning first yielded object
var promise = yieldedObject.value; //getting promise from value property of the yielded object
promise.then(function(val){ //callback for then() of promise
    return val.json(); //getting json stream from fetch response
}).then(function(val){ //chaining another then()
    var secondYieldedObject = genObject.next(val.title); //sending title back to generator function
                                                         //and receiving second yielded object from generator function
    var secondPromise = secondYieldedObject.value; //getting promise from value property of second yielded object
    secondPromise.then(function(val){ //callback for then() of promise
       return val.json();  //getting json stream from fetch response
    }).then(function(val){ //chaining another then()
      genObject.next(val.title); //sending back the second title to the generator function
    })
})
```

The code inside the generator function is clean and readable, however all the iterating 
code below it is a mess. Luckily, there is a recursive method for iterating through promises 
that will be covered on the next page.

---

#### Module 4 - Generators   Using Generators with Asynchronous Functions   Recursive Method to Iterate through Promises

# Recursive Method for Iterating through Promises

A **recursive** function may be used to iterate through yielded `Promises` and return their fulfillment values back to 
the Generator function.

Notice how a recursive function is used to handle yields to `Promises` and yields to other values in any order:

```javascript
function run(genFunc){
    const genObject = genFunc(); //creating a generator object

    function iterate(iteration){ //recursive function to iterate through promises
        if(iteration.done) //stop iterating when done and return the final value wrapped in a promise
            return Promise.resolve(iteration.value);
        return Promise.resolve(iteration.value) //returns a promise with its then() and catch() methods filled
          .then(x => iterate(genObject.next(x))) //calls recursive function on the next value to be iterated
          .catch(x => iterate(genObject.throw(x))); //throws an error if a rejection is encountered
    }

    try {
        return iterate(genObject.next()); //starts the recursive loop
    } catch (ex) {
        return Promise.reject(ex); //returns a rejected promise if an exception is caught
    }
}
```

The `run()` function shown above takes in a Generator function as an argument and uses the recursive `iterate()` 
function to process through all of the Generator function's yield statements. 

1. If a `Promise` is ***yielded***, the fulfillment value of that `Promise` is sent back to the Generator function. 
2. If an integer, string or object is ***yielded***, then those values are sent back as is to the Generator function.

Notice how the `run()` function is used to process a Generator function:

```javascript
function *gen(){

        var post1Stream = yield fetch("https://jsonplaceholder.typicode.com/posts/1");
        var post1 = yield post1Stream.json();
        console.log(post1.title); 
        //post1.title = "sunt aut facere repellat provident occaecati excepturi optio reprehenderit"
        var post2Stream = yield fetch("https://jsonplaceholder.typicode.com/posts/2");
        var post2 = yield post2Stream.json();
        console.log(post2.title);
        //post2.title = "qui est esse"

        var number = yield 12345;
        console.log(number)
        //number = 12345

        var string = yield "abc";
        console.log(string)
        //string = "abc"

        var obj = yield {id:123,name:"xyz"};
        console.log(obj)
        //obj = Object {id:123,name:"xyz"}

        var a = yield 54434337746;
        console.log(a);
        return "done";

}

run(gen).then(x => console.log(x)) //logs "done"
        .catch(x => console.log(x.message));
```

If a rejected Promise is yielded, the `run()` method will stop iterating through the Generator function and return a rejected `Promise`.

Notice how the `run()` method handles rejected promises:

```javascript
function *gen(){

        var post1Stream = yield fetch("https://jsonplaceholder.typicode.com/posts/1");
        var post1 = yield post1Stream.json();
        console.log(post1.title); 
        //post1.title = "sunt aut facere repellat provident occaecati excepturi optio reprehenderit"
        var post2Stream = yield fetch("https://jsonplaceholder.typicode.com/posts/2");
        var post2 = yield post2Stream.json();
        console.log(post2.title);
        //post2.title = "qui est esse"

        var error = yield Promise.reject(Error("error message!"));
        //error thrown here, generator function terminates

        var number = yield 12345;
        console.log(number); //doesn't occur because an earlier promise was rejected 

        return 'done'; //doesn't occur because an earlier promise was rejected

}

run(gen).then(x => console.log(x))
        .catch(err => console.log(err.message); //logs "error message!" from the rejected Promise
```

---


#### Module 4 - Generators   Using Generators with Asynchronous Functions   Code Demo

# Code Demo: Using Generators with Asynchronous Functions

https://youtu.be/Mfq-wqFyl2s

> Hello, in this video, I will be introducing the topic of generators,
> and will be giving an overview on what will be covered in this module.
> In short, generators are functions that can be paused and resumed. In
> addition, generators can send output when pausing and receive input
> when resuming. Now the reason why generators are such a big deal and
> why you should use them is because of how well they work with
> asynchronous functions, such as fetch. Imagine this. You have a
> generator function, and inside it you wanna make some asynchronous
> spec requests. Now you can make the fetch call, pause the generator,
> wait for the fetch response to process outside of the generator, and
> then return the fetch fulfillment value back to the generator before
> resuming it. This allows for asynchronous code to be written inside
> the generator synchronously, and that's what's amazing about
> generators. In this module, I will be covering how to create a
> generator function, how to pause and restart a generator by iterating
> through it, and how to send output and receive inputs into a
> generator. And lastly, I'll show you how to use generators with
> asynchronous promises.

---

#### Module 4 - Generators   Module 4 Tutorial Lab: Star Wars API   Tutorial 4 Intro Video

# Tutorial 4 Intro Video


> Hello, in this video, I will be introducing the assignment for this
> module, which is to build a web application that uses the Star Wars
> API to allow users to find out which characters were in each Star Wars
> movie. Generators will be used to simplify the writing of asynchronous
> code. Let's start by looking at how the completed application should
> function. The web application is simple, it should have an input field
> for the Star Wars episode number and a search button next to it.
> Pressing the search button will populate the film name and character
> section of the application. The Star Wars API takes a couple of
> seconds to finish processing. But eventually it finishes. This
> tutorial is meant to teach you how to use generators with promises to
> write more readable asynchronous code. Please read the tutorial pages
> for the exact details on how to create the application. Good luck, and
> happy coding.

---

#### Module 4 - Generators   Module 4 Tutorial Lab: Star Wars API   Tutorial 4 Guidelines

# Tutorial 4 Guidelines

The tutorial for this module is designed to teach you how to use Generator Functions with the Fetch API to Fetch character data from the Star Wars API.

For a demo of the application, download the reference solution files below and run starwars.html in a browser. You can right click the links below and press Save link as... to download them.

Reference Solution:

[starwars.html](https://courses.edx.org/asset-v1:Microsoft+DEV234x+3T2017+type@asset+block/starwars.html)  (this demo won't work if you just click on it, you must download the files)

[starwars.js](https://d37djvu3ytnwxt.cloudfront.net/assets/courseware/v1/386b127cacc9665a5db4347fcac31267/asset-v1:Microsoft+DEV234x+3T2017+type@asset+block/starwars.js)

![demo](https://d37djvu3ytnwxt.cloudfront.net/assets/courseware/v1/180a87420e5ea8e98dfa568f8b0dc37f/asset-v1:Microsoft+DEV234x+3T2017+type@asset+block/starwars_movie_demo.gif)

---

#### Module 4 - Generators   Module 4 Tutorial Lab: Star Wars API   Tutorial Lab: Star Wars API

# Tutorial Lab: Star Wars API

To get started, create two files in the same directory:

* `starwars.html` - this will hold our HTML code
* `starwars.js` - this will hold our JavaScript code

Next, familiarize yourself with the documentation on the Star Wars API.

Documentation on the Starwars API can be found here: https://swapi.co/documentation

The URL endpoints of the films can be accessed at http://swapi.co/api/films/id 

The URL endpoints of the characters can be accessed at  http://swapi.co/api/people/id 

Example: http://swapi.co/api/films/1

Example: http://swapi.co/api/people/1

In Part 1 of the tutorial, we will build the following visual elements of our application:

* header label
* input field
* search button
* paragraph section for film title
* paragraph section for the list of characters

In starwars.html, enter the following code to get started:

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>


        <script src="starwars.js"></script> 
    </body>
</html>
```

The above code sets up the basic HTML structure and links the HTML to the JavaScript file in the directory.

Next, in the body of the HTML, add the following code to add a header label, input field, search button, film paragraph section, and character paragraph section to the application:

```html
<h3> Enter Film Number (1-7) </h3>
<input id = "input">
<button id = "button">Search</button>
<p id="filmsText">Film:<p>
<p id="peopleText">Characters:<p>
```

Run the `starwars.html` file in the browser to verify that the visual elements appear as shown below:

![start](https://d37djvu3ytnwxt.cloudfront.net/assets/courseware/v1/04904e791d2b40f81fe241479c77efcb/asset-v1:Microsoft+DEV234x+3T2017+type@asset+block/img4-1.PNG)

In Part 2 of the tutorial, we will edit the JavaScript code in `starwars.js` to do the following:

* populate the film and character paragraph sections when a film number is searched

To accomplish this, we need to first create a function called `run(genFunc)` that will help us iterate through Generator functions.

Add the `run(genFunc)` function definition inside the starwars.js file:

```javascript
function run(genFunc){
    const genObject= genFunc(); //creating a generator object

    function iterate(iteration){ //recursive function to iterate through promises
        if(iteration.done) //stop iterating when done and return the final value wrapped in a promise
            return Promise.resolve(iteration.value);
        return Promise.resolve(iteration.value) //returns a promise with its then() and catch() methods filled
        .then(x => iterate(genObject.next(x))) //calls recursive function on the next value to be iterated
        .catch(x => iterate(genObject.throw(x))); //throws an error if a rejection is encountered
    }

    try {
        return iterate(genObject.next()); //starts the recursive loop
    } catch (ex) {
        return Promise.reject(ex); //returns a rejected promise if an exception is caught
    }
}
```

The `run(genFunc)` function makes yielded asynchronous functions return their fulfillment values as results to the yield 
statement. This is incredibly useful as it allows asynchronous code to be written synchronously inside Generator functions.

Next, we will create a Generator function to execute the asynchronous Fetch requests that will be made.

To start, declare the `gen()` Generator function definition inside the `starwars.js` file:

```javascript
function *gen(){
    //check if input is valid


    //fetch the film


    //fetch the characters


    //display film title and characters in the film

}
```

The first step to completing the `gen()` Generator function is to check if the search input is valid. The Star Wars 
API only has data on films 1 through 7.

Add the following code under the "check if input is valid" comment:

```javascript
    //check if input is valid
    if(document.getElementById("input").value > 7 || document.getElementById("input").value < 1 ){
        throw new Error("Invalid Input - Enter a number between 1 and 7");
    }
```

The above code will throw an Error if the input value is not between 1 and 7.

The next step is to Fetch the film JSON object from the Star Wars API server.

Add the following code under the "fetch the film" comment:

```javascript
    //fetch the film
    var filmResponse = yield fetch("http://swapi.co/api/films/" + document.getElementById("input").value);
    var film = yield filmResponse.json();
```

In the above code, the yielded Fetch request returns a Response Object. The Response Object then has a JSON 
extracted using the `json()` method. The film variable contains the JSON film data.

The next step is to Fetch all of the character data from the Star Wars API server.

Add the following code under the "fetch the characters" comment:

```javascript
    //fetch the characters
    var characters = film.characters;
    var characterString = "Characters: <br>";
    for(let i = 0; i < characters.length ; i++){
        var tempCharacterResponse = yield fetch(characters[i]);
        var tempCharacter = yield tempCharacterResponse.json();
        characterString += tempCharacter.name + "<br>";
    }
```

In the above code, the characters variable is an array containing all of the character data URL endpoints. The `characterString` 
variable is a string the holds all of the characters' names.

The above code has a for loop that does the following to all of the character data URL endpoints:

* Yields a Fetch request on the character data URL endpoint, which returns a Response Object
* Extracts the character data JSON value from the Response Object
* Adds the character name to the `characterString` variable.

The next step is to display the film title and character names to the film and character sections in the application.

Add the following code under the "display film title and film characters" comment:

```javascript
    //display film title and film characters
    document.getElementById("filmsText").innerHTML = "Film: <br>" + film.title;
    document.getElementById("peopleText").innerHTML = characterString;
```

The above code will populate the film and character sections in the application.

The last step is to add a click event handler to the search button.

Add the following code at the top of the `starwars.js` file:

```javascript
document.getElementById("button").addEventListener('click',function(){
    run(gen).catch(function(err){
        alert(err.message);
    });
})
```
The above code will add a click event handler to the search button. The click event handler will execute the `run(genFunc)` 
method with the `gen()` Generator function as an argument. If an error is caught, an alert with a error message will be displayed.

Run the `starwars.html` file in the browser to verify that the application is able to populate the film and character 
paragraph sections when a valid film number is searched. Also verify that error alerts are displayed when invalid inputs 
are searched. Please note that it should take a couple of seconds for the Star Wars API data to fetch.

![starwars](https://d37djvu3ytnwxt.cloudfront.net/assets/courseware/v1/ace0fb83ba749d71ad7dc05c9660f75f/asset-v1:Microsoft+DEV234x+3T2017+type@asset+block/img4-2.PNG)

---

#### Module 4 - Generators   Module 4 Assignment: Starship Comparison   Assignment 4 Intro Video

# Assignment 4 Intro Video

https://youtu.be/0WhZxLZJ74E

---

#### Module 4 - Generators   Module 4 Assignment: Starship Comparison   Assignment 4 Introduction

# Assignment 4 Introduction

The assignment for this module is to use the Starwars API to create a web application that compares starships.

![assignment](https://d37djvu3ytnwxt.cloudfront.net/assets/courseware/v1/936f801096672a4f02c2b5bdbedacf02/asset-v1:Microsoft+DEV234x+3T2017+type@asset+block/starships_full_demo.gif)

The user should see the following:

An application description labeled "Select two Starships from the dropdown lists to compare"
Two drop down lists full of starships
A button labeled "Compare"
A table that shows the attributes of the two starships being compared (name, cost, speed, cargo size, passengers)
The user should be able to do the following:

Populate the comparison table by selecting two starships from the drop down lists and pressing the "Compare" button. In each table row, the attribute with the higher value should have its cell highlighted red.

### Starwars API

Documentation on the Starwars API can be found here: https://swapi.co/documentation

The URL endpoint of the starships can be accessed at http://swapi.co/api.starships/id 

Example: http://swapi.co/api/starships/2

### Starship Data

The starships that should be in the dropdown lists is shown below. The number next to the starship name represents its starship id that is used in the Star Wars API.

* CR90 Corvette - 2
* V-wing - 75
* Belbullab-22 Starfighter -74
* Jedi Interceptor - 65
* Star Destroyer -3
* Trade Fedaration Cruiser - 59
* Solar Sailer -58
* Republic Attack Cruiser -63
* A-wing - 28
* B-wing -29
* Naboo Fighter -39
* Millenium Falcon -10

---

#### Module 4 - Generators   Module 4 Assignment: Starship Comparison   Assignment 4 Submission Guidelines

# Assignment 4 Submission Guidelines

### Submission Guidelines

Please submit your solution to the assignment by posting your source code in the discussions forums. A link to the appropriate section of the forums can be found at the bottom of this page. In your forum post, you may post a link to a Github repository or use an online code editor such as Codepen. Your submission will be critiqued by your peers but will not count towards your overall course grade.

### Feedback Guidelines

Please leave feedback on the assignment submissions of your peers by commenting on the following areas:

* User Interface - how well do the visual elements look?
* Functionality - does the application function as intended?
* Code - was the application coded efficiently?
* Overall Thoughts - any other comments

---

