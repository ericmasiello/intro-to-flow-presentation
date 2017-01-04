Big Themes
1. What is Flow
2. How to setup Flow
3. Flow types and syntax (101 class)
4. Using Flow with an editor Atom / Nuclide
5. Useful tools (flow-typed)
6. How to use Flow in a web applications (babel, strip types, etc)
7. How to use Flow in React Native
8. My overall reaction to it


1. What is Flow
  1. Static type checker
  1. Can be used for any type of JavaScript project
  2. Comes ready to go with React Native
2. What is static type checking?
  1. "This generally means that the type of a variable is known at compile time. For some languages this means that you as the programmer must specify what type each variable is (e.g.: Java, C, C++); other languages offer some form of type inference, the capability of the type system to deduce the type of a variable (e.g.: OCaml, Haskell, Scala) — stackoverflow.""
2. What is type checking?
  1. Strong vs. weak, dynamic vs. static
  2. What is JavaScript?
3. Why is type checking useful
  1. Autocompletion
  2. Can quickly jump to method definitions
  3. Provides type safety
  4. Removes a certain segment of errors/tests you'd otherwise have to write.
    1. Example: if i write a function that expects a type string, so long as I run flow as part of test suite, I won't need to write a unit test to handle non-string values being passed to my function
4. Typescript
  1. The other guy in the game
  2. Big in the Angular community
  3. Seems to be making traction beyond Angular
  4. I don't know much about it
5. Setting up Flow
  1. Install flow-bin `yarn add -D flow-bin`
  2. Run `./node_modules/.bin/flow init`
    1. This will create a .flowconfig file
  3. Add `/* @flow */` to files
6. Running Flow Server
  1. Add a "script" to package.json
  2. `yarn run flow` (`npm run flow`)
  3. Spawns a background process
  4. Initial startup is slow
  5. Afterward, just type `yarn run flow` and it'll be fast
7. Annotating with Flow
  // Basic annotation
  ```js
  function sayHi(name: string): string {
    return `Hi, ${name}`;
  }

  sayHi(22); // flow won't like this
  sayHi('Bill'); // all good!
  ```
8. Primitive Types:
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
14. Defining Types
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
  type ResponseEnum = 'yes' | 'no' | 'maybe';
  const myResponse: ResponseEnum = 'yes';
  const badResponse: ResponseEnum = 'blah'; // error!
  ```
19. When to annotate
  1. "Flow has sophisticated type inference and it is often unnecessary to explicitly annotate your program with types"
  2. Always annotate functions and methods
  3. If the type can be inferred based on other annotations, don't annotate.
  ```js
  function composeGreeting(name: string): string {
    return `Hi, ${name}`;
  }
  const greeting: string = composeGreeting('Eric'); // you don't need to annotate greeting as a string here
  ```
20. Type Support
  1. Has build in support for DOM, BOM, CSSOM, and Node
  2. Add additional type defs with Flow Typed
  3. Some npm modules come with flow definitions built in (e.g. React, Immutable.js)
  4. Create your own
    1. Where do you put them?
