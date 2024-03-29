title:Drag and drop DOM element bookmarklet\
subtitle:A simple javascript bookmarklet for making elements on a page draggable.\
classes: language-markup\
lang:en\
startDate:2013-11-03T21:15:55+00:00\
pubDate:2013-11-04T01:08:16+00:00

<header>
<hgroup>
Drag and drop element bookmarklet
=================================

A bookmarklet using jQuery and jQuery UI to convert any element to a draggable and droppable element.
-----------------------------------------------------------------------------------------------------

</hgroup>
</header>
Lately, for several projects I've been to needing to position many elements absolutely, by hand. I knew drag and drop functionality was easy, but didn't even try. Today, again I had to `position:absolute` a bunch of element by hand... **Enough!**. Enter a *bookmarklet* to move things around.

This bookmarklet loads from the jQuery CDN: jQuery UI and jQuery (if not already on the page). jQuery UI is to use the Draggable Widget.

![Drag and Drop Bookmarklet screenshot](img/content/draggable-bookmarklet-screenshot.png "Drag and Drop Bookmarklet screenshot")

The rest is just UI to write the selector for the elements you want to drag and drop.

notextile.

<p>
Dragg the\
<a href="javascript:(function(){if(window.draggableBookmarklet!==undefined){draggableBookmarklet();}else{document.body.appendChild(document.createElement('script')).src='https://raw.github.com/baamenabar/draggable-bookmarklet/master/drag-and-drop.js';}})();" class="boton">Draggable bookmarklet</a> to your favourites bar.

</p>
I'm opening a repo on github for this bookmarklet. Now that I've tested a few cases, I have plenty ideas for it, for starters, host the whole code in the repo, so it get updated automatically. For now it'll be just one file.

The repo on gitbu is: <https://github.com/baamenabar/draggable-bookmarklet>

This also may help with moving things around for clients at meetings.

If it's been done before, I've never seen it, I googled, and nothing, so I built it, mostly based on what was published by smashing magazine in <http://coding.smashingmagazine.com/2010/05/23/make-your-own-bookmarklets-with-jquery/>

UPDATE: I have moved the code to the repo, debugging the bookmarklet was becoming painful.
