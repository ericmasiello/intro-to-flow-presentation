Flow Video Tutorial

https://blog.aria.ai/post/why-use-flow/?utm_source=javascriptweekly&utm_medium=email
https://www.youtube.com/watch?v=xWMuAUbXcdQ&feature=share
https://hackernoon.com/type-checking-in-javascript-getting-started-with-flow-8532c11aceb3#.4krkstghn


http://djcordhose.github.io/flow-vs-typescript/flow-typescript-2.html#/

Cheat Sheet
http://www.saltycrane.com/blog/2016/06/flow-type-cheat-sheet/

- Observations from doing Flow
    - Tricky to navigate at first - a little overwhelming
    - But did in fact help me catch errors that I wouldn’t have otherwise caught until i went to run the app
        - Though they were confusing at first within the editor
        - its worth running flow command to see the full context of the error - sometimes just it highlighting something can be misleading as to the cause of the error
    - It *MAY* be useful to not add flow typings for things like React/Redux at first until you’re comfortable annotating your own code. Just cuz it may introduce some more advanced features of flow that you’re not ready to take on yet
    - When dealing with things like Redux where you have action objects that can take many forms, its much easier to enforce a uniform shape of the object for any given reducer. Otherwise you’ll end up adding a lot of guards in your code in order to shut flow checking up.


- What is Flow
- What is type checking? (See that article about static types/dynamic types, casting, etc)
    - JavaScript is dynamically typed
- Why is type checking useful
    - refactoring
    - removes a certain segment of errors/tests you’d have to write
- When would you want/not-want to use Flow
    - Probably don’t use Flow for proof of concepts / hackathons
    - Would be good for medium to large code bases
        - That said any code base can benefit from type checking
- Comparison of Flow to Typescript
    - ???
- How to setup Flow
    - .flowconfig
    - Webpack
        - Types have to be stripped away to run in browser
    - Adding annotations
    - Add eslint
    - How to run flow
        - using CLI
        - webpack
        - npm scripts on pre-commit hook?
- Review types
    - long list of types to review
- Other:
    - Using Flow with React
    - Using Flow with React Native
        - (Comes built in - you just have to add annotations)
    - How to check 3rd party code (https://flowtype.org/docs/third-party.html#_)

About Flow
- Flow is a static type checker
    - “This generally means that the type of a variable is known at compile time. For some languages this means that you as the programmer must specify what type each variable is (e.g.: Java, C, C++); other languages offer some form of type inference, the capability of the type system to deduce the type of a variable (e.g.: OCaml, Haskell, Scala) — stackoverflow.”
- Using Flow in an Editor
    - There’s support for other tools
    - It comes built into Nuclide which is a package for Atom
        - The Nuclide one will give you realtime feedback as you hit save on each file
- Why Use Flow
    - Autocompletion
    - Ability to jump to method/function definitions
    - Type safety


More Flow Notes
- All you strictly need is to add /* @flow */ Some type can be inferred but others can’t. (+ vs. *) file:///Users/eric/Downloads/us.sitesucker.mac.sitesucker/flowtype.org/docs/type-annotations.html#_
- To improve performance, you should add annotations to the boundaries of modules file:///Users/eric/Downloads/us.sitesucker.mac.sitesucker/flowtype.org/docs/type-annotations.html#module-boundaries
- “any” annotations is the universal “leave me alone, I know what I’m doing” annotation
- Checking 3rd part code file:///Users/eric/Downloads/us.sitesucker.mac.sitesucker/flowtype.org/docs/third-party.html#_
    - Create library definitions “libdef”
    - Use the “flow-typed” library, it includes several 3rd party libraries https://github.com/flowtype/flow-typed/tree/master/definitions/npm
    - npm install -g flow-typed
    - npm install
    - flow-typed install
    - For compatible libraries, they are added to the flow-typed/ directory. For those it can’t find, it’ll add stubs
    - Run “flow-typed update” to check for periodic updates to flow type definitions
    - Additionally, if the flow definitions are already bundled with the npm package, it will just ignore them (e.g. React, Immutable JS, etc.)
- Flow annotations need to be stripped out using babel’s babel-plugin-transform-flow-strip-types
    - Is this true for React Native? does it somehow handle that for us?
- can make arguments optional by adding “?” e.g. function foo(x?) {}
    - you’ll need to guard against these by checking for null/undefined in the function body
    - that or just pass a default for x, e.g. function foo(x=0) {}
- .flowconfig acts as the anchor for where to start flow checking
- types
    - arrays
        - number[]
        - Array<string> (alternate form)
    - objects
        - let object: {foo: string, bar: number} = {foo: "foo", bar: 0};
        - Strict objects ONLY allow the properties listed in the type
			type StrictUser = {| name: string, age: number |};
        - objects as maps/hash
            - let coolRating: {[id:string]: number} = {};
            - coolRating["sam"] = 10;
            -
            - type MapOfNumbers = { [key: string]: number };
            - var numbers: MapOfNumbers = {
            -   ten: 10,
            -   twenty: 20,
            - };
        - define a reusable type:
            - type Person = {
            -   name: string,
            -   age: number,
            - };
        - catch all generic objects (similar to any)
            - var anyObject: Object = {};
    - functions
        - Function is the catch all type
        - Or can annotate the parameters and return type
        - When passing a function as an argument, can you do something like this as the type: theCallback (id: number) => Promise<string>
        - If you wish to return nothing, set return type to void
        - Can create optional properties (as opposed to nullable/maybes) like so
			function optional_fun(foo?: string) {
			  (foo: string|void);
			}
			optional_fun("foo");
			optional_fun(undefined);
			optional_fun();
        - Overloading is handled with unions
    - Class (instances)
        - Creating a class using class MyClass {} makes it a type you annotate
        - const instanceOfMyClass: MyClass = new MyClass();
        - “Just like other languages with classes, Flow enforces that the type of an overridden method in a superclass (e.g., bar in C) is compatible with the type of an overriding method (e.g., barin D).” file:///Users/eric/Downloads/us.sitesucker.mac.sitesucker/flowtype.org/docs/classes.html#_
        - Flow requires exported definitions be more thoroughly annotated than ones used only locally.
        - classes match nominally, not structurally. Interfaces and objects can match structurally
    - Class (just the class as is, not the instance of it)
        - const TheClass: Class<MyClass> = MyClass;
    - Tuple
        - tuples of arrays of a heterogeneous type and of a fixed length
        - var tuple_of_str_and_num: [string, number] = ["Hi", 42];
    - Enums
        - type ResponseEnum = 'yes' | 'no' | 'maybe';
        - var myResponse: ResponseEnum = ‘yes’;
    - Interface
        - exists in other languages. Allows you to define classes as sharing a common interface
    - Nullable (Maybe types) - preface with ?
        - var optional_array_of_num: ?number[] = null;
        - Maybe types have the type T|void|null for some type T.
		function maybe_fun(foo: ?string) {
		  (foo: string|void|null);
		}
        -
    - Primitives
        - :string, :boolean, :number, :null (for null), :void (for undefined)
    - Any - get outta jail. This is a super type of all types
    - Mixed - I don’t understand the difference between Any and Mixed
        - Mixed in similar to Any but is stricter. It will allow for a mixture of types (number, string, function, etc). but it validates that the thing must exist
        - e.g.
        - var x = {};
        - var method: any = x.someMethod
        - method(); // flow won’t throw any errors
        - var y = {};
        - var otherMethod: mixed = x.someMethod; // Flow won’t like this!
    - Define your own types
        - type ArrayString = Array<string>;
        - const arrStrings: ArrayString = [];
        - arrStrings.push('hi');
- “Flow has sophisticated type inference and it is often unnecessary to explicitly annotate your program with types”
- Using Flow
    - flow annotations have to be stripped away
    - Use babel 6.x with transform-flow-strip-types plugin
    - Usage
        - var foo: string = ‘Hello World’;
        - function sum(x: number, y: number): number { }
        - var numArr: number[] = [];
- API Support
    - Has build in support for DOM, BOM, CSSOM, and Node
- Maybe as an exercise, make a To Do List app using Flow
- An optional and a maybe are different: optionals allow for undefined (or no value) to be passed. Optionals pair nicely with es6 default arguments. Maybes allow you to pass null but not undefined. You can combine the two but that's probably not a great idea. https://medium.com/@wietsevenema/flowtype-understanding-optionals-and-null-2fa7b98210ce
-

Things flow is good for
- If you try to import a path to a file that doesn’t exist flow will catch it. Particularly useful when using something like Atom/Nuclide cuz it’ll highlight the error right away.
-
