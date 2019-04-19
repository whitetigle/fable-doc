---
title: Start a new project using templates
---

## Start a new project

Now we're ready, let's start a new project using Fable!

<ul class="textual-steps">
<li>

**Download latest Fable template**

The most common way is to download a *template* using git. There are several templates ready to use.

* **Browser project**

    * Simple HTML5 canvas and Browser DOM

        `git clone https://github.com/fable-compiler/fable2-samples/tree/master/browser`

    * More complex browser app with fetch & promises & json parsing

        `git clone https://github.com/fable-compiler/fable2-samples/tree/master/promises`

* **React project**

    * [Elm](https://elm-lang.org/) like [single page application (SPA)](https://en.wikipedia.org/wiki/Single-page_application) with React:

        `git clone https://github.com/fable-compiler/fable2-samples/tree/master/minimal`

    * Start a frontend app SPA using Bulma & React

        `git clone https://github.com/MangelMaxime/fulma-demo`

* **Node.js project**

    * Node.js app with fetch & promises

        `git clone https://github.com/fable-compiler/fable2-samples/tree/master/nodejs`

    * Bundled Node.js app with fetch & promises

        `git clone https://github.com/fable-compiler/fable2-samples/tree/master/nodejsbundle`

* **Advanced templates**

    * Browser app with fetch & promises & json parsing using .paket manager to resolve .NET dependencies

        `git clone https://github.com/fable-compiler/fable2-samples/tree/master/withpaket`

</li>

<li>

**Install JS dependencies**

The JS dependencies are located in the package.json file. Since we're using JS libraries, we'll need to install them

- using npm: `npm install`
- using yarn: `yarn install`

It will create a `node_modules` folder and a `package-lock.json` file. 

*** A note about `package-lock.json`
The `package-lock.json` file should be committed to ensure reproducible builds whenever anybody clones the repo.

</li>

<li>

**Build & Run & Update the app**

Now we're done the dependencies, let's start our app in watch mode. Depending on the kind of template the instructions my differ.

:::info
Always read the `README.md` file shipped in a template so that you always get things to work properly.
:::

**Web app**

For a web app, unless advised, it will always be

- using npm: `npm start`
- using yarn: `yarn start`

Then you'll be able to access your project from [http://localhost:8080/](http://localhost:8080/). So it's time to open this address in your favorite browser.

If you now open the project with your favourite code editor, you can make some changes in the `App.fs` file located in the `src` folder. Save the changes and if compilation succeeds you should be able to see your changes directly in your browser.

**Node.js app**

For a Node.js app, unless advised, it will always be

- using npm: `npm build`
- using yarn: `yarn build`

Then you'll be able see the generated JS files in the `build` folder.

:::info
In general, check the `README.md` and `package.json` to get more information about the project and other build targets.
:::

Last but not least, if you wish to understand how all the things in the templates work, what is webpack, why do we use a `.fsproj` and so on, please consider reading the [Blueprint of a common Fable project](https://github.com/fable-compiler/fable-doc/wiki/Start-a-new-project-from-scratch) which will guide you through the tools used and allow you to understand more easily Fable projects.

</li>
</ul>
