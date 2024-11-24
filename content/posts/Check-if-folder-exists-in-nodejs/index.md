---
title: "Nodejs v10 fsPromises: Let's check if a dir exists, the modern way."
subtitle: In nodejs you sometimes need to check if a given path is an actual folder or not, since node 10, you can do it with promises, along with a lot of other file system operations.
date: 2018-07-11T15:35:40-07:00
tags: ['node', 'promises', 'async', 'javascript']
---

# Check if folder exists in node, asynchronously with promises, native.

Since Node v10.0.0 fs has experimental promises support, to test that, we want to see if a folder exists and is accessible. Wi will start from the Synchronous operation, and we will end up taking advantage of `async await` to deal with the promises. Only one thing to note: this is flagged as experimental, and we will get something like: `(node:578) ExperimentalWarning: The fs.promises API is experimental` .

## Not only that, pretty much all fs methods are available with a promises interface.

## The setup

We will use the `fs.stat` method, in all it's different flavours if the path does not exist or is not accessible, you get an error: `ENOENT`. Also make sure your environment is running on node v10 or older. **v10 goes LTS in just a few months**, and in node v10 you can use `fs` with promises.

First let's make the directory and file structure we will test.

    mkdir -p uploads/memes
    echo "some content for the file">uploads/memes/not-folder
    touch stat.js

Open `stat.js` and import right away both versions of `fs`. Also set the paths to be tested, we want to check the following 3 cases:

-   1\. A valid directory
-   2\. A folder does not exist (fs.stat should throw an error)
-   3\. A file; path resolves to something but it is not a dir

```
const fs = require("fs");\
const fsPromises = require("fs").promises;

const pathsToTest = \[\
"uploads/memes",\
"nopes/memes",\
"uploads/memes/not-folder",\
\];

function isErrorNotFound(err) {\
return err.code === "ENOENT";\
}
```

Also we will define an **error checker function** that will be reused by all the methods we build.

Before we start with the actual methods we will setup **looping functions** that will pick up the array of paths and ask the given method if each of the paths is a directory, then **print the method used and the result**. For each fs.stat variation we will use a slightly different looper, but the principle stands.

## The simple way: fs.statSync

```
function isFolder_sync(path) {\
try {\
const stat = fs.statSync(path);\
return stat.isDirectory();\
} catch (err) {\
// if it's simply a not found error\
if (isErrorNotFound(err)) {\
return false;\
}\
//otherwise throw the error\
throw err;\
}\
}

/\*\* Looper function \*/\
function checkPathsSync(method) {\
console.log(\`\${method.name}: \`, ...pathsToTest.map(path =&gt; method(path)));\
}

checkPathsSync(isFolder_sync);\
//isFolder_sync: true false false
```

The core idea is: we call `fs.statSync` inside a `try catch` block to handle the case where the dir is not found, if the error is something else we don't recognize, we still throw.

## The classic Node: fs.stat (with a callback)

```
function isFolder_callback(path, callback) {\
fs.stat(path, (err, stat) =&gt; {\
if (err) {\
const errorToReturn = isErrorNotFound(err) ? undefined : err;\
callback(errorToReturn, false);\
return;\
}\
callback(undefined, stat.isDirectory());\
});\
}

function checkPaths(method) {\
pathsToTest.forEach(path =&gt;\
method(path, (err, result) =&gt;\
console.log(\`\${method.name}: \`, err ? err : result)\
)\
);\
}

checkPaths(isFolder_callback);\
// isFolder_callback: true\
// isFolder_callback: false\
// isFolder_callback: false
```

Callbacks will catch errors, so no need for the try catch. Personally I find Callbacks very pretty in simple use cases.

## The new approach: promises enabled fs.stat

```
function isFolder_promise(path) {\
return fsPromises\
.stat(path)\
.then(fsStat =&gt; {\
return fsStat.isDirectory();\
})\
.catch(err =&gt; {\
if (isErrorNotFound(err)) {\
return false;\
}\
throw err;\
});\
}

function checkPromisedPaths(method) {\
Promise.all(pathsToTest.map(path =&gt; method(path))).then(values =&gt; {\
console.log(\`\${method.name}: \`, values);\
});\
}

checkPromisedPaths(isFolder_promise);\
// (node:877) ExperimentalWarning: The fs.promises API is experimental\
// isFolder_promise: \[ true, false, false \]
```

Same idea as before, catch the not found error, if anything else is caught, throw a fuss with the error.

## To wrap up: Async Await.

#### because, why not?

```
async function isFolder_asyncAwait(path) {\
// the result can be either false (from the caught error) or it can be an fs.stats object\
const result = await fsPromises.stat(path).catch(err =&gt; {\
if (isErrorNotFound(err)) {\
return false;\
}\
throw err;\
});

return !result ? result : result.isDirectory();\
}

checkPromisedPaths(isFolder_asyncAwait);\
// isFolder_asyncAwait: \[ true, false, false \]
```

This was a fun actual implementation of `async await`, I had not used it so far, beyond the typical hello world.

### What can be improved?

well, the `isErrorNotFound` method should be taking care of `throw`ing a fit if the path is not finding anything. Also I'm sure I can come up with more elegant versions of the implementations, but I'm happy with this as a POC.

The whole code can be found as a gist at: <https://gist.github.com/baamenabar/d639055caaacd5dbd139c302caf8a4f9>

## Resources

Main resource is the node docs: <https://nodejs.org/api/fs.html#fs_fs_promises_api> ...aaand of course [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).
