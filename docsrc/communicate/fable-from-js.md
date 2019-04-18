---
title: Call Fable from JavaScript
---

[[toc]]

Sometimes, we'd like to use the power of Fable into our JavaScript apps. For instance, to create a new
[js node in Node-RED](https://nodered.org/docs/creating-nodes/first-node) or call some handy F# code from our new Node.js serverless function or even call some powerful json parsing into our JavaScript app.

It may allow you to play with Fable and add features, one at a time. So what does it take to call Fable from JavaScript?

### Call fable from your web app

If your project is a web project and you're using webpack, it just takes 2 lines of code in the webpack configuration in the `output` section of `module.exports`:

```js
libraryTarget: 'var',
library: 'MyFableLib'
```

For instance:

```js
    output: {
        path: path.join(__dirname, "./public"),
        filename: "bundle.js",
        libraryTarget: 'var',
        library: 'MyFableLib'
    },
```

This just tell webpack that we want our Fable code to be available from a `MyFableLib` context. That's it!

#### Let's try!
Let's create a very simple Fable app:

```fsharp
module App
open Fable.Core
let sayHelloFable() = "Hello Fable!"
```

In our webpack config let's add:

```js
libraryTarget: 'var',
library: 'OMGFable'
```

Now let's try this directly in our `index.html` file:
```html
<body>
    <script src="bundle.js"></script>
    <script type="text/JavaScript">
      alert( OMGFable.sayHelloFable() );
    </script>
</body>
```

Et voilà! We're done! You can find a [full sample here](https://github.com/fable-compiler/fable2-samples/tree/master/interopFableFromJS).

### Call Fable from your Node.js app
Basically it's the same thing. If you want to see a complete sample using a `commonjs` output instead of `var` (the one we used for the web app sample, please [check this project](https://github.com/fable-compiler/fable2-samples/tree/master/nodejsbundle).

There you'll see that we've added the following lines to the webpack config:

```js
    library:"app",
    libraryTarget: 'commonjs'
```

and then you can call your code from JavaScript like this:

```js
let app = require("./App.js");
```

### Learn more about webpack `libraryTarget`
If you want to know what your options are, please consult the [official documentation](https://webpack.js.org/configuration/output/#outputlibrarytarget).
