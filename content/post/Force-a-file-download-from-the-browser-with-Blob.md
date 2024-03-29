title:Force a file download from the browser with Blob\
subtitle:The browser will always try to read data you give it.\
classes: language-markup\
pubDate:2018-02-13T12:33:23-01:00

<header>
<hgroup>
Force a file download from the browser with Blob
================================================

The browser will always try to read data you give it.
-----------------------------------------------------

</hgroup>
</header>
You have a bunch of data you just generated in JavaScrip and you want to force the user to download it as a file. How to do it?

TL;DR: {#ac2}
------

From small to medium files (something that fits in your RAM) take advantage of the data URI of <a> elements. And this is how the spec expects it to be handled.\
https://www.w3.org/TR/FileAPI/\#requirements

    <a href="#" class="js-download-button">download</a>

So for an existing anchor element with a class js-download-button we would do something like the following:

### DOM naive approach:

just create an anchor element and put the data in the href attribute.\
https://ourcodeworld.com/articles/read/189/how-to-create-a-file-and-generate-a-download-with-javascript-in-the-browser-without-a-server

    const downloadButton = document.querySelector('.js-download-button');
    downloadButton.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent({sone: 'object', with: 'data'}));
    downloadButton.setAttribute('download', ‘myJsonFileName.JSON');

### JS approach:

Take advantage of the .createObjectURL(blob) method and the Blob API. MDN has a good example on this approach.\
https://developer.mozilla.org/en-US/docs/Web/API/File/Using\_files\_from\_web\_applications\#Example\_Using\_object\_URLs\_to\_display\_images

**note that:** Each time you call createObjectURL(), a new object URL is created, even if you've already created one for the same object. Each of these must be released by calling URL.revokeObjectURL() when you no longer need them. Browsers will release these automatically when the document is unloaded; however, for optimal performance and memory usage, if there are safe times when you can explicitly unload them, you should do so. ()\
(from https://stackoverflow.com/a/33542499/537314 )

it would look like this:

bc.. let contentBlob;

function saveAsJson(fileName, data) {\
jsonString = JSON.stringify(data, null, 2);

// resetting if you do this more than once, or you'll get a memory leak\
if (contentBlob) {\
window.URL.revokeObjectURL(contentBlob);\
contentBlob = undefined;\
}

// populate the download button\
const downloadButton = document.querySelector('.js-download-button');\
contentBlob = new Blob(\[jsonString\], {type : 'application/json'});\
downloadButton.href = window.URL.createObjectURL(contentBlob);\
downloadButton.download = fileName + (new Date().getTime()) + '.JSON';\
}

saveAsJson('FILE-NAME-', {sone: 'object', with: 'data'});

But if you want the foolproof, all cases handled version use the FileSave.js lib.\
https://github.com/eligrey/FileSaver.js/blob/master/src/FileSaver.js

If you have huge files, you are probably doing it wrong, but take a look at the Streams API, and this library: ( https://github.com/jimmywarting/StreamSaver.js ).
