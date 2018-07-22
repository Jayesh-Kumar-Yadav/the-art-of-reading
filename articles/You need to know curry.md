## Functions are first-class citizen

Functions are first-class citizen in JavaScript, as the same as other types(e.g. number, array, object). They can be used as arguments, as well as return value from other function.

Take a simple example, if we aim to print out all the elements in an array, most people probably will do it this way:

```javascript
function printWithLoop(arr) {
  for (let i = 0, len = arr.length; i < len; i++) {
    console.log(arr[i]);
  }
}
```

If you're a bit familar with higher-order function, you may be inclined to use [Array.prototype.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach):

```javascript
function printWithIterator(arr) {
  (arr || []).forEach(it => {
    console.log(it);
  });
}
```

We can then simplify the code further with：

```javascript
function simplePrint(arr) {
  (arr || []).forEach(console.log);
}
```

**Have a second thought here, is the output from _simplePrint_ exactly the same as _printWithIterator_? If not, can you explain what makes the difference?**

### Function overloading

Function overloading gives the ability for functions to behave differently based on the number or type of the arugments. E.g. [Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from).

- When given a single argument, it simplely creates a new Array instance from an array-like or iterable object

```javascript
let set = new Set(["foo", "bar", "foo"]);
console.log(Array.from(set)); //["foo", "bar"]
```

- When given a second argument _mapFn_ which is optioinal, it will apply _mapFn_ to each element when creating the new array.

```javascript
console.log(Array.from([1, 2, 3], x => x + x)); // [2, 4, 6]
```

## Curry

Curry, also called partial application. Currying a function basically means that a function will absord some arguments and return another function for later invocation. The returning function can have access to the already absorded arguments through [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures).

### Parameters vs Arguments

First we need to understand two basic concepts in functions.

- Parameters: the placeholders in function declarations. We can use _function.length_ to get the number of parameters.

```javascript
function A(a, b) {}
// a and b are parameters
console.log(A.length); //2
```

- Arguments: the values passed to functions when functions are applied. We can use _arguments_ to get the list of arguments.

```javascript
function B(a, b) {
  console.log(arguments);
}

B(1, 2, 3); // 1,2,3
```

To conclude, parameters are what you _expect_ while arguments are what you _got_.

### Curry example

Assume we have a function to compute the sum of three numbers.

```javascript
function sum(x, y, z) {
  console.log(x + y + z);
}

sum(1, 2, 3); //6
```

If we want to achieve the following result:

```javascript
sum(1, 2, 3); //6
sum(1)(2, 3); //6
sum(1, 2)(3); //6
```

Have a deep look, what we want to achieve is that when the function _sum_ receives the arguments it expects (i.e. three numbers), it will compute their sum and return the value. Otherwise, it will keep returning another function (e.g. sum(1) and sum(1,2) both return another function) which can be invoked with more numbers. This is Curry!

```javascript
function curry(fn) {
  //Let's ignore the function context for simplicity
  return function f(...args) {
    /**
     * if the number of passed in arguments is more than what we expect
     * invoke the function and return the result
     */
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    } else {
      //return another function which can access to the passed arguments through closure
      return function(...arr) {
        return f.apply(this, args.concat(arr));
      };
    }
  };
}

let sumWithCurry = curry(sum);
sumWithCurry(1, 2, 3); //6
sumWithCurry(1)(2, 3); //6
sumWithCurry(1, 2)(3); //6
```

### [Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

[Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) has two main functionalities:

- binding the function context _this_
- curry

```javascript
function sayHi(greeting, ending) {
  console.log(`My name is ${this.name}, ${greeting}. ${ending}!`);
}

let fn = sayHi.bind({ name: "mike" }, "Love you"); // greeting is absorded
fn("Thanks!"); // My name is mike, Love you. Thanks!!
```

In development, we can use curry to write more elegant code:

```javascript
function print(arr) {
  console.log(arr.join("|"));
}

let arr = [1, 2, 3];
setTimeout(function() {
  print([1, 2, 3]);
}, 1000);
// 1|2|3
```

is equivalent to

```javascript
function print(arr) {
  console.log(arr.join("|"));
}

let arr = [1, 2, 3];
setTimeout(print.bind(null, arr), 1000);
// 1|2|3
```

## Reference

- [Effective JavaScript](https://www.amazon.com/Effective-JavaScript-Specific-Software-Development/dp/0321812182/ref=sr_1_3?s=books&ie=UTF8&qid=1521248523&sr=1-3&keywords=Effective+JavaScript)

## Notice

- If you want to follow the latest news/articles for the series of reading notes, Please [「Watch」](https://github.com/n0ruSh/the-art-of-reading)to Subscribe.
