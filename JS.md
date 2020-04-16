# JS Interview Prep

## Hoisting

JavaScript only hoists declarations, not initializations.

Example 1 gives same result as Example 2:

```
x = 5; // Assign 5 to x

elem = document.getElementById("demo"); // Find an element
elem.innerHTML = x;                     // Display x in the element

var x; // Declare x
```

```
var x; // Declare x
x = 5; // Assign 5 to x

elem = document.getElementById("demo"); // Find an element
elem.innerHTML = x;                     // Display x in the element
```

`let` & `const` are not hoisted

## Difference between Arrow functions & Regular functions

### Arguments Binding

Arrow functions do not have an arguments binding. However, they have access to the arguments object of the closest
non-arrow parent function. Named and rest parameters are heavily relied upon to capture the arguments passed to
arrow functions.

### Use of `this` keyword

Unlike regular functions, arrow functions do not have their own `this`. The value of `this` inside an arrow function
remains the same throughout the lifecycle of the function and is always bound to the value of `this` in the closest
non-arrow parent function.

```
let me = {
 name: "Ashutosh Verma",
 thisInArrow:() => {
 console.log("My name is " + this.name); // no 'this' binding here
 },
 thisInRegular(){
 console.log("My name is " + this.name); // 'this' binding works here
 }
};
me.thisInArrow();
me.thisInRegular();
```

### Using `new` keyword

Regular functions created using function declarations or expressions are constructible and callable. Since regular functions are constructible, they can be called using the `new` keyword.
However, the arrow functions are only callable and not constructible, i.e arrow functions can never be used as constructor functions. Hence, they can never be invoked with the `new` keyword

```
let add = (x, y) => console.log(x + y);
new add(2,3);
```

Above will throw: `Uncaught TypeError: add is not a constructor at...`

## Difference between `let` & `var`

Main difference is scoping rules. Variables declared by var keyword are scoped to the immediate function body (hence the function scope) while let variables are scoped to the immediate enclosing block denoted by { } (hence the block scope).

```
function run() {
  var foo = "Foo";
  let bar = "Bar";

  console.log(foo, bar);

  {
    let baz = "Bazz";
    console.log(baz);
  }

  console.log(baz); // ReferenceError
}

run();
```

## Is a `const` variable immutable?

The const declaration creates a read-only reference to a value. It does not mean the value it holds is immutable, just that the variable identifier cannot be reassigned.

## Event Bubbling

When you click on a button, the event passes from inner event target to Document. Click event pass in the following order:

1. `button`
2. `div`
3. `body`
4. `HTML`
5. `Document`

`event.stopPropagation()` will stop event bubbling from occurring.

## Event Delegation

```
const character = document.getElementById("disney-character");
character.addEventListener('click', showCharactersName);
```

In our code above, on page load, the event listener finds an HTML element with the id disney-character and sets a click event listener on that HTML element.
This works fine if the element exists on the page when the page is loaded. However what happens to the event listener when the element is added to the DOM (webpage) after the initial page load?

The whole idea behind event delegation is that instead of listening for a change on the inputs directly, we should look for an HTML element that is going to be on the page when the page initially loads.

```
<ul class=”characters”>
</ul>
<script>
  function toggleDone (event) {
    console.log(event.target)
  }
  const characterList = document.querySelector('.characters')
  characterList.addEventListener('click', toggleDone)
</script>
```

The event.currentTarget identifies the current target for the event, as the event traverses the DOM. It always refers to the element to which the event listener has been attached. In our case the event listener was attached to the unordered list, characters, so that is what we see in our console.

#### Writing Event Delegation in JavaScript

Because we now know that the EVENT.TARGET identifies the HTML elements on which the event occurred, and we also know what element we want to listen for (the input element), solving this in JavaScript is relatively easy.

```
//Event Delegation
function toggleDone (event) {
  if (!event.target.matches(‘input’)) return
  console.log(event.target)
  //We now have the correct input - we can manipulate the node here
}
```

Basically the code above states, if the event target that was clicked DOES NOT match the input element, exit the function.
If the event target that was clicked DOES match the input element, console.log the event.target and execute subsequent JavaScript code on that child node.
This is important, now we can be confident that a user clicked the correct child node, even though the inputs were added to the DOM after the initial page load.

## What is Type Safety?

Type safety means that the compiler will validate types while compiling, and throw an error if you try to assign the wrong type to a variable.

## What's the difference between strongly types and weakly typed languages?

The main difference, roughly speaking, between a strongly typed language and a weakly typed one is that a weakly typed one makes conversions between unrelated types implicitly, while a strongly typed one typically disallows implicit conversions between unrelated types

## What is software coupling?

- Coupling is the indication of the relationships between modules.
- Coupling shows the relative dependence/interdependence among the modules.
- Coupling is a degree to which a component / module is connected to the other modules.
- While designing you should strive for low coupling i.e. dependency between modules should be less
- Making private fields, private methods and non public classes provides loose coupling.
- Coupling is Inter-Module Concept.

## Functional Programming & Currying

This function takes three numbers, multiplies the numbers and returns the result.

```
function multiply(a, b, c) {
    return a * b * c;
}
multiply(1,2,3); // 6
```

See, how we called the multiply function with the arguments in full. Let’s create a curried version of the function and see how we would call the same function (and get the same result) in a series of calls:

```
function multiply(a) {
    return (b) => {
        return (c) => {
            return a * b * c
        }
    }
}
log(multiply(1)(2)(3)) // 6
```

We could separate this multiply(1)(2)(3) to understand it better:

```
const mul1 = multiply(1);
const mul2 = mul1(2);
const result = mul2(3);
log(result); // 6
```