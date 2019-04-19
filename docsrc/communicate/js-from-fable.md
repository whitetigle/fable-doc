---
title: Call JS from Fable
---

[[toc]]

## Call JS from Fable

Interoperability is a matter of trust between your statically typed F# code and your untyped dynamic JS code. In order to mitigate risks, Fable gives you several possibilities, amongst them: type safety. But sometimes it may sound more convenient to just call JS code in a more dynamic fashion although it often leads to runtime bugs which Fable is very good at decimating from the ground up.

We'll describe both the safe way and the dynamic way and then it will be up to you to decide. Let's start!

### Adding the JS library to the project

The very first thing to do is add the library to our project. Since we always have a `package.json` file, we'll just add the wanted library to our project using either `npm install myAwesomeJSLibrary` or `yarn add myAwesomeJSLibrary`. The library will then be available in the `node_modules` folder that is automatically used by Fable.

If your library is a custom one, just skip this step.

### Type safety with Imports and Interfaces

To use code from JS libraries first you need to import it into F#. For this Fables uses [ES2015 imports](https://developer.mozilla.org/en/docs/web/JavaScript/reference/statements/import), which can be later transformed to other JS module systems like `commonjs` or `amd` by Babel.

There are two ways to declare ES2015 imports in the Fable: by using either the **Import attribute** or the **import expressions**. The `ImportAttribute` can decorate members, types or modules and works as follows:

```fsharp
// Namespace imports
[<Import("*", from="my-module")>]
// import * from "my-module"

// Member imports
[<Import("myFunction", from="my-module")>]
// import { myFunction } from "my-module"

// Default imports
[<Import("default", from="express")>]
// import Express from "express"
```

If the value is globally accessible in JS, you can use the `Global` attribute with an optional name parameter instead;

```fsharp
 let [<Global>] console: JS.Console = jsNative
```

#### Let's practice! 1st try!

Now we've seen this, let's review the sample described in the [interop](https://github.com/fable-compiler/fable2-samples/tree/master/interop) sample

Let's say we have an `alert.js` file that we'd like to use in our Fable project.
```js
function triggerAlert(message) {
  alert(message);
}

const someString = "And I Like that!";

export { triggerAlert, someString };
```

As you can see we've got one function, `triggerAlert` and one constant `someString`. Both are exported using the ES6 `export` keyword.

In order to use this in our Fable code, let's create an `interface` that will mimic this:

```fsharp
  open Fable.Core.JsInterop // needed to call interop tools

  type IAlert =
    abstract triggerAlert : message:string -> unit
    abstract someString: string
```

As you can see the process is quite easy. The `I` in `IAlert` is not mandatory but it's a precious hint that we're going to use an interface. The `abstract` keyword only indicates that there's no actual implementation in F#. That's true, since we rely on the JavaScript one.

Now let's use this:
```fsharp
  [<Import("*", "path/to/alert.js")>]
  let mylib: IAlert = jsNative
```

Here we use the `Import` directive, just like we described earlier.
- step 1: We specify the elements we wish to use. Here`*` means: "take everything that's been exported"
- step 2: we set the path to our js library.
- step 3: we create a let binding called `mylib` to map the js library.
- step 4: we use the `jsNative` keyword to say that our `mylib` is just a placeholder for the JavaScript native implementation.

Now we can use this:

```fsharp
mylib.triggerAlert ("Hey I'm calling my js library from Fable > " + mylib.someString)
```

If everything's working correctly, it should create an alert popup in your browser! (Of course this sample is intended for web apps, but you could do the same in a Node.js app.

#### Let's practice! 2nd try!

Assuming we've got two exported functions from a `Canvas.js` file: `drawSmiley` and `drawBubble`:

```js
export function drawSmiley() {
  // do something
}
export function drawBubble() {
  // do something
}
```

we could use the same method we used with `alert.js`:

```fsharp
  open Fable.Core.JsInterop // needed to call interop tools

  type ICanvas =
    abstract drawSmiley: unit -> unit
    abstract drawBubble: unit -> unit

  [<Import("*", "path/to/Canvas.js")>]
  let mylib: ICanvas = jsNative

  mylib.drawSmiley() // etc..
```

or we could do the same things a little bit differently, by using the `importMember` helper function to directly map the js function to the F# function.

```fsharp
open Fable.Core.JsInterop // needed to call interop tools

module Canvas =
  // here we just import a member function from canvas.js called drawSmiley.
  let drawSmiley: id:string -> unit = importMember  "path/to/Canvas.js"
  let drawBubble: id:string -> unit = importMember  "path/to/Canvas.js"

Canvas.drawSmiley()
```

The result would be the same, but the philosophy is slightly different. That's basically up to you to make a choice 😉

#### Miscelaneous import helpers
There are other interop helpers you can use thanks to `Fable.Core.JsInterop`:

```fsharp
open Fable.Core.JsInterop

let buttons: obj = importAll "my-lib/buttons"
// import * as buttons from "my-lib/buttons"

// It works for function declarations too
let getTheme(x: int): IInterface = importDefault "my-lib"
// import getTheme from "my-lib"

let myString: string = importMember "my-lib"
// import { myString } from "my-lib"

// Use just `import` to make the member name explicit
// as with the ImportAttribute
let aDifferentName: string = import "myString" "my-lib"
// import { myString } from "my-lib"
```

### Emit, your relatively safe friend

You can use the `Emit` attribute to decorate a function. Every call to the function will then be replaced inline by the content of the attribute with the placeholders `$0, $1, $2...` replaced by the arguments. For example, the following code will generate JavaScript as seen below.

```fsharp
open Fable.Core

[<Emit("$0 + $1")>]
let add (x: int) (y: string): float = jsNative

let result = add 1 "2"
```

When you don't know the exact number of arguments you can use the following syntax:

```fsharp
type Test() =
    [<Emit("$0($1...)")>]
    member __.Invoke([<ParamArray>] args: int[]): obj = jsNative
```

It's also possible to pass syntax conditioned to optional parameters:

```fsharp
type Test() =
    [<Emit("$0[$1]{{=$2}}")>]
    member __.Item with get(): float = jsNative and set(v: float): unit = jsNative

    // This syntax means: if second arg evals to true in JS print 'i' and nothing otherwise
    [<Emit("new RegExp($0,'g{{$1?i:}}')")>]
    member __.ParseRegex(pattern: string, ?ignoreCase: bool): Regex = jsNative
```

The content of `Emit` will actually be parsed by Babel so it will still be validated somehow. However, it's not advised to abuse this method, as the code in the template will remain obscure to Fable and may prevent some optimizations.

#### Let's do it! Use Emit

Now let's work with Emit and take a new example with the following `MyClass.js`:

```js
class MyClass {
  constructor( value ) {
    this.awesomeInteger = value;
  }

  get awesomeInteger() {
    return this._awesomeInteger;
  }

  set awesomeInteger( newValue ) {
    this._awesomeInteger = newValue;
  }

  isAwesome() {
    return this._awesomeInteger === 42;
  }

  static getPI() {
    return Math.PI;
  }
}

export { MyClass as default}
```
 Let's list its members:
- an `awesomeInteger` member which is an int with a getter and a setter
- a method, `isAwesome`, that checks if our `awesomeInteger` equals 42
- a static method `getPi()` that just returns the value of `Math.PI`

Here's the Fable implementation. Let's start with the members:

```fsharp
type MyClassImplementation =
  abstract awesomeInteger: int with get, set
  abstract isAwesome: unit -> bool
```

Now we need to be able to call our static function. We'll also fit our constructor here.

```fsharp
type MyClass =
  [<Emit("new $0($1...)")>]
  abstract Create : awesomeInteger:int ->  MyClassImplementation
  abstract getPI : unit-> float
```

Here we used the `Emit` attribute to call the contructor of `MyClass`. Last but not least, let call this:

```fsharp
[<Import("default", "../public/MyClass.js")>] // 3
let myClassStatic : MyClass = jsNative
```

Now it's possible to use our class. Let's see the complete code:

```fsharp

type MyClassImplementation = // 1
  abstract awesomeInteger: int with get, set
  abstract isAwesome: unit -> bool

type MyClass = // 2
  [<Emit("new $0($1...)")>]
  abstract Create : awesomeInteger:int ->  MyClassImplementation //= jsNative  // takes a string parameter and does not return anything
  abstract getPI : unit-> float

[<Import("default", "../public/MyClass.js")>] // 3
let myClassStatic : MyClass = jsNative

// let's make our object mutable to be able to change its members
let mutable myObject = myClassStatic.Create 40

// using getter
let whatDoIget = myObject.awesomeInteger // using getter
mylib.triggerAlert ("Hey I'm calling my js class from Fable. It gives " + (string whatDoIget))

// using setter
myObject.awesomeInteger <- 42
mylib.triggerAlert ("Now it's better. It gives " + (string myObject.awesomeInteger))

// calling member function
mylib.triggerAlert ("Isn't it awesome? " + (string (myObject.isAwesome())))

// call our static function
mylib.triggerAlert ("PI is " + (string (myClassStatic.getPI())))
```

### StringEnum, Enums compiled to strings!
Like TypeScript where it's possible to define String Literal Types which are similar to enumerations with an underlying string value. Fable allows the same feature by using union types and the StringEnum attribute. These union types must not have any data fields as they will be compiled to a string matching the name of the union case.

By default, the compiled string will have the first letter lowered. If you want to prevent this or use a different text than the union case name, use the CompiledName attribute:

```fsharp
open Fable.Core

[<StringEnum>]
type MyStrings =
    | Vertical
    | [<CompiledName("Horizontal")>] Horizontal

myLib.myMethod(Vertical, Horizontal)
```

```js
// js output
myLib.myMethod("vertical", "Horizontal")
```

### Plain Old JavaScript Objects

To create a plain JS object (aka POJO), use `createObj`:

```fsharp
open Fable.Core.JsInterop

let data =
    createObj [
        "todos" ==> Storage.fetch()
        "editedTodo" ==> None
        "visibility" ==> "all"
    ]
```

### Dynamic typing: don't read this!

Through the use of the tools we just described above, Fable guarantees you shouldn't run into nasty bugs because all the code will be compiled. It it does not compile it either means your js library does not exists or its path is not good or that your F# implementation laks something. We do rely on Fable on systems that are used 24/7, web apps or Node.js apps. We know that if it compiles, it means a 99% chance of running without any problems.

Our motto is: `If it compiles, it works!`

Still, like we stated, **interop is a question of trust**. If you trust your js code and F# code, then maybe it's ok to do things together without further checks. Maybe.

:::warning
Disclaimer: use this at your own risks
:::

#### What is dynamic typing?
Fable.Core.JsInterop implements the F# dynamic operators so you can easily access an object property by name (without static check) as follows:

```fsharp
open Fable.Core.JsInterop

printfn "Value: %O" jsObject?myProperty

let pname = "myProperty"

printfn "Value: %O" jsObject?(pname) // Access with a string reference

jsObject?myProperty <- 5 // Assignment is also possible
```

When you combine the dynamic operator with application, Fable will destructure tuple arguments as with normal method calls. These operations can also be chained to replicate JS fluent APIs.

```fsharp
let result = jsObject?myMethod(1, 2)
// var result = jsObject.myMethod(1, 2)

chart
    ?width(768.)
    ?height(480.)
    ?group(speedSumGroup)
    ?on("renderlet", fun chart ->
        chart?selectAll("rect")?on("click", fun sender args ->
            Browser.console.log("click!", args))

// will generate the following js code
// chart
//     .width(768)
//     .height(480)
//     .group(speedSumGroup)
//     .on("renderlet", function (chart) {
//         return chart.selectAll("rect").on("click", function (sender, args) {
//             return console.log("click!", args);
//         });
//      });
```

> Note that in order to make this possible, the output of the ? is an applicable value. If you don't want this behaviour, `unbox` or the `!!` dynamic cast operator: `let myValue: int = !!myObj?otherMethod("foo", "bar")`

When you have to call a function with the new keyword in JS, use `createNew`.

```fsharp
open Fable.Core.JsInterop
let instance = createNew jsObject?myMethod(1, 2)
```

If you prefer member extensions rather than operators for dynamic typing, you can open `Fable.Core.DynamicExtensions` to have the methods `.Item` and `.Invoke` available on any object.

```fsharp
open Fable.Core.DynamicExtensions

let foo = obj()
let bar1 = foo.["b"]  // Same as foo.Item("b")
foo.["c"] <- 14
let bar2 = foo.Invoke(4, "a")
```
