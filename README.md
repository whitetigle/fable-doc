# fable-doc
Fable documentation web site.

The current documentation is a Work In Progress and not ready yet to be used.

## How to build the documenation?

1. `npm install`
2. `npm run nacara`
3. By default Nacara is serving the documentation at : `http://0.0.0.0:8080`

If the port is taken please take a look at the console output, Nacara will tell you which port has been choosen.

## How to use Nacara

Because Nacara is still young here is somes tips / advices.

1. Before sending a PR, please follows this steps:

    1. Delete the `docs` folder
    2. Run `npm run nacara`
    3. Wait a few seconds until Nacara is done and kill it.
    4. The `docs` folder now contains the ready to be publish website

2. If you make a change to `nacara.json` for adding a new page, menu, navbar item. You need to restart Nacara in order to see the changes

3. If you have an issue/bug, you can always take a look at [Nacara issues](https://github.com/MangelMaxime/Nacara/issues) and check if it's already tracked or no.

4. If you are stuck, feel free to contact @MangelMaxime (on Github, Gitter or the F# Slack), he do is best to help you :)
