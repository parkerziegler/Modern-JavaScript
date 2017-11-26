# Modern JavaScript Notes
Notes for Tyler McGinnis' Modern JavaScript Course. Check it out [here](https://tylermcginnis.com/courses/modern-javascript/).

## ECMAScript, TC39, and the Standardization Process
- 1995: Netscape Navigator is the standard for sharing documents, but folks are imagining client interactivity.
- Goal - create a scripting language for the browser that is easy for non-Java developers to use.
- JavaScript was originally named Mocha and then LiveScript. The "Java" prefix came along to ride the popularity of Java at the time.
- Microsoft was developing JScript in parallel to Mocha / LiveScript, resulting in different implementations of JavaScript.
- ECMA came along to assist in the standardization process of JavaScript.
- Each iteration of JavaScript comes with a standard and a committee at EMCA that reviews that standard.
- ECMAScript is used to refer to the official standard (**ECMA-262**), **TC-39** is the official committee working on ECMA-262.
- Goal of TC-39 – Create, improve, or deny new proposals to the JavaScript language.
- All proposals go through stages
    - **Stage 0 (Strawman)** - planned to be presented or was presented and not totally rejected.
    - **Stage 1 (Proposal)**** - a TC-39 member needs to introduce it, needs to describe the problem it solves and provide a high level API.
    - **Stage 2 (Draft)** - All aspects of the proposal get locked down, and there is a more specific description of the proposal's syntax and semantics.
    - **Stage 3 (Candidate)** - Spec text is finished, 2 spec complient implementations are created.
    - **Stage 4 (Finished)** - tests have to be written for the proposal, 2 spec implementations pass these tests, proposal is merged into next release of ECMAScript.
- TC-39 is now following a yearly release schedule, so Stage 4 features are now released every year (i.e. ES2016, ES2017, etc.).

## var vs. let vs. const: Variable declarations in ES6 | ES2015
- Two new ways to create variables in ES6 - `let` and `const`
- Variable declarations vs. initialization
    - **Declaration** just involves declaring a variable, i.e. `var declaration;`
    - **Initialization** sets that variable equal to a value, i.e. `var initialization = 'initialization';`
- **Scope** - where variables are accessible in your application
    - Global Scope - when variables are declared in the global scope they become part of the global object (i.e. `window`)
    - Function Scope - when variables are declared inside a `function` they are scoped to that function or any nested functions

```javascript
function discountPrices (prices, discount) {

    var discounted = [];
    for (var i = 0; i < prices.length; i++) {

        var discountedPrice = prices[i] * (1 - discount);
        var finalPrice = Math.round(discountedPrice * 100) / 100;

        discounted.push(finalPrice);

    }

    /* we can access these variables because var is function scoped */
    console.log(i);
    console.log(discountedPrice);
    console.log(finalPrice);

    return discounted;
}

discountPrices([100, 200, 300], 0.5); // [50, 100, 150]
```

- **Hoisting** - all variable declarations are moved to the top of the current scope.

```javascript
// code like this
console.log(hoisted); // undefined
var hoisted;

// gets interpreted as this
var hoisted;
console.log(hoisted); // undefined
```

- So the above function `discountPrice` gets interpreted as this:
```javascript
function discountPrices(prices, discount) {
    var discounted;
    var i;
    var discountedPrice;
    var finalPrice;

    discounted = [];
    for (i = 0; i < prices.length; i++) {
        discountedPrice = prices[i] * (1 - discount);
        finalPrice = Math.round(discountedPrice * 100) / 100;

        discounted.push(finalPrice);
    }

    console.log(i);
    console.log(discountedPrice);
    console.log(finalPrice);

    return discounted;
}
```

- If a variable is not declared with `var`, the JavaScript engine will keep looking up scopes until it reaches the global scope. If it reaches the global scope and finds no `var` declaration, it will add the variable as a property on the global scope. This is **not recommended**.

- `let`
    - `let` is block-scoped to the block where it was created or any nested block

```javascript
function discountPrices (prices, discount) {

    let discounted = [];
    for (let i = 0; i < prices.length; i++) {

        let discountedPrice = prices[i] * (1 - discount);
        let finalPrice = Math.round(discountedPrice * 100) / 100;

        discounted.push(finalPrice);

    }

    /* we cannot access these variables because let is block-scoped */
    console.log(i); // ReferenceError: i is not defined.
    console.log(discountedPrice); // ReferenceError: discountedPrice is not defined.
    console.log(finalPrice); // ReferenceError: finalPrice is not defined.

    return discounted;
}

discountPrices([100, 200, 300], 0.5); // [50, 100, 150]
```

- `let` declarations are hoisted to the top of the block, however, attempting to access a `let` variable before it is defined will result in a `ReferenceError`.
- `const`
    - `const` is almost the same as `let`, but you cannot reassigna a variable declared with `const`.
    - **Important note:** you can change a property created on a `const` object because you are not reassigning the variable decalred with `const` itself.
- Always use `const` unless the variable is going to change.

## Object and Array Destructuring in JavaScript | ES6 | ES2015

- In **ES5**, we could not extract properties from an object. **ES6** fixes this.
```javascript
const user = {
    name: 'Parker',
    handle: '@parker_ziegler',
    location: 'Seattle, WA'
};

const { name, handle, location } = user;
```
- We can even destructure the results of a function.
```javascript
function getUser() {

    return {
        name: 'Parker',
        handle: '@parker_ziegler',
        location: 'Seattle, WA'
    };
}

const { name, handle, location } = getUser();
```
- We can also destructure arrays.
```javascript

const user = ['Parker', 'Ellis', 'Ziegler'];
const [first, middle, last] = user;

const csv = 'Ziegler, Parker, Ellis';
const [last, first, middle] = csv.split(',');
```

- There are some other more advanced parts of destructuring. For example, we can rename Object properties when destructuring.
```javascript
const user = {
    n: 'Parker Ziegler',
    h: '@parker_ziegler',
    l: 'Seattle, WA'
};

const { n: name, h: handle, l:location } = user;
console.log(name); // 'Parker Ziegler'
console.log(handle); // '@parker_ziegler'
console.log(location); // 'Seattle, WA'
```
- We can also destructure our function arguments.
```javascript
function fetchRepos ({ language='All', minStars=0, maxStars=0, createdBefore=new Date().getTime(), createdAfter=null }) {
    // do something with args
}

/* we can pass an object to the function. we also don't need to 
specify args we don't care about if we specify default args! */
fetchRepos({
    language: 'JavaScript',
    minStars: 100,
    createdBefore: new Date('01/01/2017').getTime()
});
```
- The benefit here is that we pass a whole object as the argument, so the order of our arguments does not matter.
- We also no longer need to specify arguments we don't care about, i.e. there's no need to pass `null` for any args.
- We can set default properties on args as well!

## Shorthand Property and Method Names in JavaScript | ES6 | ES2015
- Shorthand Property - if `key` and `value` name are the same, we can just write them once.
- Method Names - we can omit the `function` keyword altogether.
```javascript
function formatMessage (name, id, avatar) {

    /* since key and value names match here, we only need to
    write them once */

    return {
        name,
        id,
        avatar,
        save() {
            // save method body
        }
    };
}
```

## Computed Property Names in JavaScript | ES6 | ES2015
- "Have an expression be computed as a property name on an object."
```javascript
function objectify (key, value) {
    return {
        [key]: value
    };
}
```

## Template Literals (Template Strings) in JavaScript | ES6 | ES2015
- Template literals are great for string interpolation or multiline strings.
```javascript
function welcomeMsg (fName, lName, email) {

    return `Hello ${fName} ${lName}. The email you used to sign up is "${email}"`;
}
```
- You can even embed HTML markup in multiline strings. This becomes useful for things like writing GraphQL schemas.
```javascript
function welcomeTemplate (fName, lName, email) {
    return `
        <div>
            <h1>Hello, ${fName} ${lName}</h1>
            <i>The email you used to sign up is "${email}"</i>
        </div>
    `;
}
```

## Arrow Functions in JavaScript | ES6 | ES2015
- Two main benefits - concision and how they deal with `this` keyword.

- A simple arrow function:
```javascript
const add = (x, y) => x + y;
```

- Iteration:
```javascript
users.map(user => user + " great");
```

- Async requests and filtering:
```javascript
function getTweets (uid) {
    return fetch('https://api.users.com/' + uid)
        .then(response => response.json)
        .then(response => respone.data)
        .then(tweets => tweets.filter(tweet => tweet.stars > 50))
        .then(tweets => tweets.filter(tweet => tweet.rts > 50))
}
```

- Dealing with the `this` keyword:
```javascript

// inside a React component
componentDidMount() {

    /* there is no need to bind this here. our arrow function
    preserves the this context of the component. */
    api.fetchPopularRepos(lang)
        .then(repos => {
            /* using parens here allows us to implicitly return
            the new state here */
            this.setState(() => ({
                repos
            }));
        });
}
```

## Default Parameters in JavaScript | ES6 | ES2015
- Allows you to define default values for function parameters when a parameter is not specified during function invocation.
- We can also use default parameters to ensure that certain args are passed to certain functions, i.e. by defining an error handler as our default value on required parameters.

```javascript
function isRequired (name) {
    throw new Error(name + 'is required.');
}

function calculatePayment (price = isRequired('price'), salesTax = 0.047, discount = 0) {

    // function body
}
```

## Compiling vs. Polyfills with Babel
- There's a delay between when standards are introduced, approved, and implemented in browsers. A transpiler like `babel` can help to transpile modern ES6 code back to ES5.
- However, `babel` will not always get our code all the way there. Sometimes we need *polyfills* to fill in these gaps.
- **Compiling** – run syntax through various syntax transforms.
- **Polyfilling** - add new methods or objects to the global JavaScript object. It's adding new functionality to the browser.

## ES6 imports, exports, modules, and named imports
- In ES6, you can `export` more than one item from a module. These are called *named exports*. You `import` these *named exports*b y specifying them as *named imports*.
```javascript
// math.js
export function add (x, y) {
    return x + y;
}

export function multiply(x, y) {
    return x * y;
}

// main.js
import { add, multiply } from './math';
```
- Use `import * as X` syntax for importing everything from a module under a particular variable name.
```javascript
// main.js
import * as math from './math';

math.add(1, 2); // 3
math.multiply(1, 2); // 2
```
- Modules can also export single values using `default`. These are *unnamed exports*.
```javascript
// add.js
export default function add (x, y) {
    return x + y;
}

// main.js
import add from './math';
```
- You can mix *named* and *unnamed exports*.
```javascript
// math.js
export function add (x, y) {
    return x + y;
}

export default function multiply(x, y) {
    return x * y;
}

// main.js
import multiply, { add } from './math';
```

## `async` / `await` in React
- The main benefit of `async` / `await` syntax is that it allows you to write asynchronous code in a seemingly synchronous manner.
```javascript
// our promise
function getUser () {
    return new Promise((resolve, reject ) => {
        setTimeout(() => resolve({ name: 'Parker' }), 2000)
    });
}

async function handleGetUser () {

    try {
        const user = await getUser();
        console.log(user); // { name: 'Parker' }
    } catch (error) {
        console.log('Error in handleGetUser', error);
    }
}
```