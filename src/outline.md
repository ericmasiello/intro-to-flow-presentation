Big Themes
1. What is Flow
2. How to setup Flow
3. Flow types and syntax (101 class)
4. Using Flow with an editor Atom / Nuclide
5. Useful tools (flow-typed)
6. How to use Flow in a web applications (babel, strip types, etc)
7. How to use Flow in React Native
8. My overall reaction to it

https://flowtype.org/try/


1. What is Flow
  1. Static type checker
  1. Can be used for any type of JavaScript project
  2. Comes ready to go with React Native
2. What is static type checking?
  1. "This generally means that the type of a variable is known at compile time. For some languages this means that you as the programmer must specify what type each variable is (e.g.: Java, C, C++); other languages offer some form of type inference, the capability of the type system to deduce the type of a variable (e.g.: OCaml, Haskell, Scala) — stackoverflow.""
3. What is type checking?
  1. Strong vs. weak, dynamic vs. static
  2. What is JavaScript?
4. Why is type checking useful
  1. Autocompletion
  2. Can quickly jump to method definitions
  3. Provides type safety
  4. Removes a certain segment of errors/tests you'd otherwise have to write.
    1. Example: if I write a function that expects a type string, so long as I run flow as part of test suite, I won't need to write a unit test to handle non-string values being passed to my function
  5. [REFERENCE pyramid.jpg]
5. Typescript
  1. The other guy in the game
  2. Big in the Angular community
  3. Seems to be making traction beyond Angular
  4. I don't know much about it
6. Setting up Flow
  1. Install flow-bin `yarn add -D flow-bin`
  2. Run `./node_modules/.bin/flow init`
    1. This will create a .flowconfig file
  3. Add `/* @flow */` to files
7. Running Flow Server
  1. Add a "script" to package.json
  2. `yarn run flow` (`npm run flow`)
  3. Spawns a background process
  4. Initial startup is slow
  5. Afterward, just type `yarn run flow` and it'll be fast
8. Annotating with Flow
  // Basic annotation
  ```js
  function sayHi(name: string): string {
    return `Hi, ${name}`;
  }

  sayHi(22); // flow won't like this
  sayHi('Bill'); // all good!
  ```
9. Primitive Types:
  1. number
  2. string
  3. boolean
  4. null
  5. void (undefined)
9. Optionals
  1. Flow makes a distinction between null and undefined. null is its own type. void = undefined
  2. `undefined` can be useful when you have an optional parameter
  ```js
  function sayHi(name?: string): string {}
  function sayHi(name = 'Eric') {}
  function sayHi(name?: string = 'Eric'): string {}
  ```
  3. optionals can be though of as `T | void` (where T is the type)
  4. There's another type called a maybe (can be thought of as `T | void | null`). Probably not as useful as an optional
10. `any` vs. `mixed`
  1. `any` is the shutup, i know what im doing leave me along type. Its basically a way to just tell flow not to type check a particular value
  2. `mixed` is similar in that it can be any type, but it still requires you add the necessary checks before using it.
  ```js
  function(x: any): void {
    console.log(x.y.z);
  }
  ```
  ```js
  function(x: mixed): void {
    console.log(x.y.z); // flow would complain
  }

  function(x: mixed): void {
    if(x && x.y) {
      console.log(x.y.z); // now we're good
    }    
  }
  ```
11. Arrays
  1. Can define arrays of any type
  ```js
  function addNumbers(values: number[]): number {
    //...
  }
  ```
12. Objects
  1. Generic objects
  ```js
  let myObj: Object = {foo: "foo", bar: 0};
  ```
  2. Non-strict objects:
  ```js
  let myObj: {foo: string, bar: number} = {foo: "foo", bar: 0, baz: true};
  ```
  3. Strict objects: ONLY allow the properties listed in the type
  ```js
  let myObj: {|foo: string, bar: number|} = {foo: "foo", bar: 0};
  ```
13. Using Objects as maps
  ```js
  let compSciCourses = {
    CS101: 'Computer Science 101: Intro to C',
    CS102: 'Computer Science 102: Intro to C++',
    CS201: 'Computer Science 201: Intro to Java',
    CS202: 'Computer Science 202: Advanced Java'
  }
  ```
  1. Adding annotations:
  ```js
  let compSciCourses: {[id:string]: string} = {
    CS101: 'Computer Science 101: Intro to C',
    CS102: 'Computer Science 102: Intro to C++',
    CS201: 'Computer Science 201: Intro to Java',
    CS202: 'Computer Science 202: Advanced Java'
  }
  ```
14. Defining Types (Type Aliases)
  1. Writing types, especially elaborate ones like for objects or call back functions can make your code difficult to read
  2. Can define your own types:
  ```js
  type MapOfNumbers = { [key: string]: number };
  var numbers: MapOfNumbers = {
    ten: 10,
    twenty: 20,
  };

  type Person = {
    firstName: string,
    lastName: string
  };
  let eric = {
    firstName: 'Eric',
    lastName: 'Masiello'
  };

  type ArrayString = string[];
  const arrStrings: ArrayString = ['Hello', 'World'];
  ```
15. Functions
  1. Similar to `Object`, `Function` is the catch all type
  2. Or can annotate the parameters and return type
  ```js
  type Person = {
    firstName: string,
    lastName: string
  };
  type LookupPersonFn = (id: number) => Person;

  function greetPerson(id: number, lookupMethod: LookupPersonFn): string {
    const person = lookupMethod(id);
    return `Welcome, ${person.firstName} ${person.lastName}`;
  }
  ```
16. Classes
  ```js
  class Person {
    firstName: string,
    lastName: string,

    constructor(firstName: string, lastName: string) {
      this.firstName = firstName;
      this.lastName = lastName;
    }
  }

  const presenter: Person = new Person('Eric', 'Masiello');
  console.log(presenter.firstName, presenter.lastName);

  const organizer = new Person('Srijak', 'Rijal'); // don't need to annotate
  ```
17. Polymorphic classes
  1. I won't go much into this but you can do it. [Show an example]
18. Special things you can do with Flow
  1. Define Tuple: tuples of arrays of a heterogeneous type and of a fixed length
  ```js
  const tuple_of_str_and_num: [string, number] = ['Hi', 42];
  ```
  2. Define an Enum
  ```js
  type ResponseEnum = 'yes' | 'no' | 'maybe'; // this is a "union" type
  const myResponse: ResponseEnum = 'yes';
  const badResponse: ResponseEnum = 'blah'; // error!

  type Person = {
    age: number,
    name: string,
    gender: 'male' | 'female'
  }

  const person: Person = {
    name: 'joe',
    age: 10,
    gender: 'male'
  };
  console.log(person.job)
  ```
19. When to annotate
  1. "Flow has sophisticated type inference and it is often unnecessary to explicitly annotate your program with types"
  "Flow requires annotations at the boundaries of modules. This allows Flow to analyze modules in isolation which improves the performance of checking types across module boundaries. We’ve found that this helps to improve the self-documenting nature of module interfaces as well."
  "You can type annotate all your code. That would be the most expressive and self-documenting approach. However, Flow does a lot of type inference for you to alleviate this requirement when it becomes a burden. The only place that you must annotate types is where those types go across module boundaries."
  2. Always annotate functions and methods
  3. If the type can be inferred based on other annotations, don't annotate.
  ```js
  var b: boolean = true; // not necessary.

  function composeGreeting(name: string): string {
    return `Hi, ${name}`;
  }
  const greeting: string = composeGreeting('Eric'); // you don't need to annotate greeting as a string here
  ```
20. When to annotate (continued)
  Annotate the boundaries of your CommonJS/ES modules
  ```js
  export function add(num1: number, num2: number): number {
    return num1 + num2;
  };
  ```
  You can also `import` or `export` type annotations
  ```js
  //User.js
  export type UserID = number;
  export type User = {
    id: UserID,
    name: string,
  };

  //some-other-file.js
  import type { UserID, User } from "./User.js";
  ```
21. Type Support
  1. Has build in support for DOM, BOM, CSSOM, and Node
  2. Add additional type defs with Flow Typed
  3. Some npm modules come with flow definitions built in (e.g. React, Immutable.js)
  4. Create your own
    1. Where do you put them?
22. Flow + Editor Plugins = Good Time
  1. Vim https://github.com/flowtype/vim-flow
  2. Emacs https://github.com/flowtype/flow-for-emacs
  3. Nuclide (for Atom) https://nuclide.io/docs/languages/flow/
  4. Sublime https://github.com/SublimeLinter/SublimeLinter-flow
  5. VS Code https://github.com/flowtype/flow-for-vscode
23. Using Flow in a Web App
  1. Requires you strip annotations away
  ```js
  function composeGreeting(name: string): string {
    return `Hi, ${name}`;
  }
  ```
  2. Using `babel` + `babel-plugin-transform-flow-strip-types`
  3. Add to your .babelrc file
  ```
  {
    "plugins": ["transform-flow-strip-types"]
  }
  ```
  4. You'll want to have flow run as part of your test coverage
  ```
  // package.json
  "scripts": {
    "lint:script": "eslint . .eslintrc.js .stylelintrc.js --ext [js,jsx] --cache",
    "lint:style": "stylelint '{src,demo}/style/**/*.scss'",
    "lint": "npm-run-all --parallel lint:script lint:style",
    "test": "cross-env NODE_ENV=test nyc mocha test/.setup.js 'src/**/*.test.jsx'",
    "flow": "flow",
    "validate": "npm-run-all --parallel flow lint test"
  }
  ```
24. Using Flow in React Native
  1. Already comes with a .flowconfig configured specifically for RN
  2. Add flow bin that matches version in your .flowconfig (e.g. 0.36.0)
  ```shell
  yarn add -D flow-bin@0.36.0
  ```  
  3. Add it to your package.json under scripts
  ```
  "scripts": {
      ...
      "flow": "flow"
  }
  ```
  4. No need to strip annotations with babel. its taken care of for you.
25. My impression so faster
  1. Its a new challenge. I enjoy it but that isn't to say its not without its frustrations.
  2. Can be challenging working with 3rd party libraries that haven't been type checked
  3. Use an editor that has support for Flow (Atom/Nuclide) - real time feedback is the most useful.
  4. Utilizing 3rd party libraries can be challenging. To get the most of out flow you'll need to use libraries that annotations or create your own annotations for those libraries. If you're staying mostly in the "core" React ecosystem, most of these libraries have annotations (React, Redux, Immutable, etc).
26. More to explore
  1. Configuring your project with .flowconfig
  2. Polymorphic classes
  3. Mixins
  4. Interfaces
  5. Utilities e.g. `$Key<T>`, `$Diff<A, B>`, etc.
