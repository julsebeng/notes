# Full Stack JavaScript Development

---

## Modern Javascript

### Block scopes, `let`, and `const` keywords

```javascript
// Function scope
function foo() {
    // Variables defined with var in function scopes aren't accessible outside of the function.
    var fcn_var = "foo";
}
console.log( fcn_var ); // ReferenceError

// Block scope
{
    // Variables defined with var inside block scopes ARE accessible outside of the block.
    var blockscope_var = "bar";
    // Variables defined with let inside block scopes AREN'T accessible outside of the block.
    let blockscope_var2 = "baz";
    // Variables defined with const behave as if they were defined with let, but are also immutable.
    const blockscope_var3 = "qux";
    blockscope_var3 = "quuux"; // TypeError
}
console.log( blockscope_var ); // Valid

// Variables in loop scope defined with var are also accessible outside of it.
if( var i = 0; i < 4; i++ )
    // loop
}
console.log( i ); // Valid
// Variables in loop scope defined with let aren't accessible outside of it.
if( let i = 0; i < 4; i++ )
    // loop
}
console.log( i ); // ReferenceError
```

Some things to be aware of:
- Objects defined with `const` will still have mutable member data, but the reference to this object itself cannot
  be changed.
    - `const` is useful when importing data from libraries, to prevent it from being modified.
    - Functions can also be defined with the `const` keyword.

### Arrow functions and enhanced object literals

```javascript

const result = ( arg1 ) => {
    // Function body
}
// One-line functions that only have one argument can be reduced even further.
const result = arg1 => arg1 + 1;

// The interpretation of "this" changes slightly when using arrow functions
exports.i = "am exports"
console.log( this ); // "{ i: 'am exports' }"
let util = {
    f1: function() {
        console.log( this );
    },
    f2: () => {
        console.log( this );
    }
}
// Interpretation of this becomes the calling object, i.e. util
util.f1(); // { f1: [Function: f1], f2: [Function: f2] }
// Interpretation of this becomes the parent object, i.e. exports
util.f2() // { i: 'am exports' }

const X = {
    PI: PI,
    sum: sum,
    square: square
};
// If the property name and its value have the same name inside some object, the syntax can be reduced:
const X = {
    PI,
    sum,
    square
};
```

### Destructuring and default/rest/spread

```javascript
// Reading properties from an object:
const square = X.square;
// If the target variable has the same name as the property, destructuring syntax can be used:
const { square } = X;
// This works with multiple properties:
const { sum, square } = X;

// Destructuring works inside function arguments as well, if an object is being passed in:
const toDecimal = ( { base, number } ) => {
    return parseInt( number, base );
};
toDecimal( { base: 2, number: 102 } );

// Default values can be used of destructured argument isn't given:
const toDecimal = ( { base = 2, number } ) => {
    return parseInt( number, base );
};
toDecimal( { number: 102 } );

// Rest Operator also valid within JS:
const toDecimal = ( base, ... numbers ) => {
    console.log( numbers ); // [ 101, 100, 1010 ]
    // Spread operator
    console.log( 0, ... numbers ); // [ 0, 101, 100, 1010 ]
};

toDecimal( 2, 101, 100, 1010 );
```

### Modules and Classes

```javascript
// Class syntax in jS:
class Footer extends Component {
    constructor( props ) {
        super( props );
    }
}
```

---

## Setup and Configuration
- Must first create a `package.json` file to store project information: `npm init`
- To save dependencies to this package, use the `--save` flag when performing an `npm install`, i.e.
` npm install --save express`
    - Alternatively, use `npm i -S express`
    - For dev dependencies, i.e. only ones that will be used for the development process and not for deploying to
      production, use `--save-dev` instead (or `-D`).
- Dependencies for a `package.json` file can be installed automatically by running `npm install` in a directory
  with a `package.json` file.
- Scripts can be added to the `package.json` file to define behavior.

```json
  "scripts": {
    // Start the node server by wrapping it in a nodemon instance; ignore public/ as changes in that directory
    // will typically be made by the running Node instance.
    "start": "nodemon --exec babel-node server.js --ignore public/",
    // Run webpack in watcher and developer mode.
    "dev": "webpack -wd"
  },
```

---

## Node Modules
- Module dependencies are brought in with `import`.
```javascript
import 'config'; // Will look for a core module named config.js, as well as any modules installed with npm.
import `./config`; // Will look for a config.js file in the current working directory.
```

- Note that imports are cached; if a module is reimported it isn't actually reevaluated.
- Every module gets its own scope; "global" variables inside of modules aren't exposed to modules that import it.
    - To expose values to importing modules, `export` is used:
```javascript
// When import 'module' is used, it imports the default exported object.
export default {
    port: env.PORT || 8080 // Default value for port is 8080
};

// Non-default objects are imported via destructuring:
// import module, { node-env } from 'module'
export const node-env = env.NODE_ENV;

// Functions can also be exported
export const foo = function() {
};

```

### The HTTP/HTTPS Modules
- Comes with Node.js as a core module, no need to `npm install` anything.
- Can act as a client or a server.
- Use HTTP for `http://` resources, HTTPS for `https://` ones.

Usage:
```javascript
// As a client: send get request, create a subscriber that reacts to chunk events.
import https from 'https';
https.get( 'https://google.com', res => {
    res.on( 'chunk', chunk => {
        console.log( chunk.toString() );
    } );
} );

// As a server: stand up a server, listens on a port, and emits signals that can be responded to.
import http from 'http';
const server = http.createServer();
server.listen( 8080 );
// Respond to events emitted when a request is made to the server.
server.on( 'request', ( req, res ) => {
    // Write back to the requester via the response stream
    res.write( "Foo\n" );
    setTimeout() => {
        res.write( "This response is sent 3 seconds after the first" )
    }, 3000 );
} );
```

### Creating a Server with Express
```javascript
import express from 'express';
import fs from 'fs';

const server = express();
// Define port and the success handler function; this listens for a single request event
server.listen( 8080, () => {
    ...
} );
// Express also offers server-side routing, the first argument being the route we're interested in and the second being the success handler.
server.get( '/', ( req, res ) => {
    fs.readFile( 'index.html', ( err, data ) => {
        res.send( data.toString() );
    } );
} );
server.get( '/about.html', ( req, res ) => {
    fs.readFile( 'about.html', ( err, data ) => {
        res.send( data.toString() );
    } );
} );
```


---

## Libraries
- `react`: framework for describing user interfaces that are a part of a Node app.
- `react-dom`: renders user interfaces in the frontend and backend.
- `express`: essentially a wrapper for the Node.js core HTTP module.
- `mongodb`: official driver for interacting with a MongoDB server from Node.
- `webpack`: structures modular code into a flat file that modern web browsers can understand.
- `nodemon`: automatically reload Node server when changes have been made to the source.
- `babel`: