## Table of Contents

- [Callbacks](#callbacks)
- [Asynchronous](#asynchronous)
- [Promises](#promises)
- [Async/Await](#async-await)

## Callbacks

**A callback is a function passed as an argument to another function, This technique allows a function to call another function. A callback function can run after another function has finished.**

**JavaScript functions are executed in the sequence they are called.**

**Sequence Control**

Sometimes you would like to have better control over when to execute a function. Suppose you want to do a calculation, and then display the result.
You could call a calculator function (myCalculator), save the result, and then call another function (myDisplayer) to display the result:

**Example 1**

```javascipt
function myDisplayer(some) {
  document.getElementById("demo").innerHTML = some;
}

function myCalculator(num1, num2) {
  let sum = num1 + num2;
  return sum;
}

let result = myCalculator(5, 5);
myDisplayer(result);
```

Or, you could call a calculator function (myCalculator), and let the calculator function call the display function (myDisplayer):

**Example 2**

```javascipt
function myDisplayer(some) {
  document.getElementById("demo").innerHTML = some;
}

function myCalculator(num1, num2) {
  let sum = num1 + num2;
  myDisplayer(sum);
}

myCalculator(5, 5);
```

**The problem with the first example above, is that you have to call two functions to display the result. The problem with the second example, is that you cannot prevent the calculator function from displaying the result.**
Now it is time to bring in a callback.

**Using a callback, you could call the calculator function (myCalculator) with a callback (myCallback), and let the calculator function run the callback after the calculation is finished:**

```javascript
// myDisplayer is a called a callback function.
function myDisplayer(some) {
  document.getElementById("demo").innerHTML = some;
}

function myCalculator(num1, num2, myCallback) {
  let sum = num1 + num2;
  myCallback(sum);
}

myCalculator(5, 5, myDisplayer);
```

**Example**
```javascript
// Create an Array
const myNumbers = [4, 1, -20, -7, 5, 9, -6];

// Call removeNeg with a callback
const posNumbers = removeNeg(myNumbers, (x) => x >= 0);

// Display Result
document.getElementById("demo").innerHTML = posNumbers;

// Keep only positive numbers
function removeNeg(numbers, callback) {
  const myArray = [];
  for (const x of numbers) {
    if (callback(x)) {
      myArray.push(x);
    }
  }
  return myArray;
}
```

## Asynchronous

**Functions running in parallel with other functions are called asynchronous. A good example is JavaScript setTimeout()**

**Example**

```javascript
setTimeout(myFunction, 3000);

function myFunction() {
  document.getElementById("demo").innerHTML = "I love You !!";
}
```

## Promises

**A Promise is an Object that links Producing code and Consuming code**, "Producing code" is code that can take some time & "Consuming code" is code that must wait for the result.

**Syntax**

```javascript
let myPromise = new Promise(function(myResolve, myReject) {
// "Producing Code" (May take some time)

  myResolve(); // when successful
  myReject();  // when error
});

// "Consuming Code" (Must wait for a fulfilled Promise)
myPromise.then(
  function(value) { /* code if successful */ },
  function(error) { /* code if some error */ }
);
```

A JavaScript Promise object can be:

* Pending
* Fulfilled
* Rejected

The Promise object supports two properties: state and result.

While a Promise object is "pending" (working), the result is undefined. When a Promise object is "fulfilled", the result is a value. When a Promise object is "rejected", the result is an error object.

To demonstrate the use of promises, we will use the callback examples from the previous chapter:

**Example Using Callback**

```javascript
setTimeout(function() { myFunction("I love You !!!"); }, 3000);

function myFunction(value) {
  document.getElementById("demo").innerHTML = value;
}
```

**Example Using Promise**

```javascript
et myPromise = new Promise(function(myResolve, myReject) {
  setTimeout(function() { myResolve("I love You !!"); }, 3000);
});

myPromise.then(function(value) {
  document.getElementById("demo").innerHTML = value;
});
```

## Async/Await

**async and await make promises easier to write, async makes a function return a Promise, await makes a function wait for a Promise**

```javascript
async function myFunction() {
  return "Hello";
}
myFunction().then(
  function(value) {myDisplayer(value);},
  function(error) {myDisplayer(error);}
);
```

**The await keyword can only be used inside an async function. The await keyword makes the function pause the execution and wait for a resolved promise before it continues:**

**Syntax**

```javascript
async function myDisplay() {
  let myPromise = new Promise(function(resolve, reject) {
    resolve("I love You !!");
  });
  document.getElementById("demo").innerHTML = await myPromise;
}

myDisplay();
```

