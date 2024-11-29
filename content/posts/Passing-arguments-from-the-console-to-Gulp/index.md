---
title: Passing arguments from the console to Gulp
summary: "In node scripts (like Gulp) you sometimes need to pass arguments, here is how."
classes: language-markup
date: 2016-04-16T19:35:20+01:00
tags: ["nodejs", "gulp", "shell"]
--- 

# Passing arguments from the console to Gulp

Node scripts you need to pass arguments, getting them can be cumbersome. {#node-scripts-you-need-to-pass-arguments-getting-them-can-be-cumbersome. .subtitle}

If you want to run a task with certain parts turned **off** or **on**, or even pass a value from the command line to gulp, by default you can’t. Well you can, but it’s a node thing: `process.argv` it is just an array, so it's a bit limited, so there is a module to abstract the mess: [yargs](https://www.npmjs.com/package/yargs) .

```sh
npm install —save-dev yargs
```

Then you require it.

```js
var argv = require('yargs').argv;
```

Now you have access to the arguments or flags passes from the console. Something like:

```sh
gulp css —verbose
```

will be available in the gulpfile as `argv.verbose`, or

```sh
gulp html —lang=es
```

will be reachable in the gulpfile with `argv.lang` and you’ll get the string.

## no dependencies option

For simple flags that are either there or not you can just filter for them, without the need to install a package.

```sh
gulp css --no-minification
```

can be checked inthe script as follows

```js
const = noMinification = process.argv.filter(item =&gt; item === 'no-minification');
```

...and that's all, it does not scale well for more configuration flags, and options, but is cleaner than adding a dependency.
