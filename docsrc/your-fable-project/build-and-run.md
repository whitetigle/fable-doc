---
title: Build & Run
---

# Build & Run

Your project is ready? Then it's time to build and run your project.

Depending on the sample you've chosen, build options may differ. Since the JavaScript ecosystem offers many different tools, they may differ according to the needs of a project. Please refer to the README file for the chosen sample to get more information.

But most of the time for we use webpack to handle the build. For this we use 2 commands:
- `webpack-dev-server`: this command **starts** the application in development mode (and adds a `#DEBUG` flag). It runs a temporary version that will watch changes and reload accordingly
- `webpack`: this command **builds** the application for a production release. It generates `.js` files.

Shortcuts to these targets are often listed in the `package.json` at the root of your project directory. But if none exist, just call them from a terminal.
