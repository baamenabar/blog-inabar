title:Flexbox for the real world: Sticky footer fallback\
subtitle: Take advantage of flexbox for layout while still providing support for older browsers.\
classes: language-markup\
startDate:2014-10-13T03:58:28+00:00\
pubDate:2014-10-16T06:24:52+00:00

<header>
<hgroup>
Flexbox for the real world: Sticky footer
=========================================

Case nº1 of graceful fallbacks for browsers without flexbox.
------------------------------------------------------------

</hgroup>
</header>
Flexbox has long been the promise of a revolution, had it been specified as it is today, when the original proposal came out, the adoption today would be complete. I've been playing with flexbox since the original syntax came out, and I loved it, specially coupled with `media-queries`, there was even a polyfill for IE. But as I tried to use it in production, I spent a week trying to figure things out, found big inconsistencies and bugs in the implementations that made it impossible to use it in production, with a disillusioned heart I dropped it. Four years later it's ready for everyday use (with the proper fall-backs).

Much has been [demoed](http://philipwalton.github.io/solved-by-flexbox/) and [written](http://css-tricks.com/snippets/css/a-guide-to-flexbox/) about how to use flexbox, it now gracefully solves problems that where impossible or too complicated to solve in CSS.

There also has been a huge effort to find out how to write CSS in order to [cater for each of the 3 implemented syntaxes](http://css-tricks.com/using-flexbox/). Today, with [autoprefixer](https://github.com/postcss/autoprefixer) all I need to write is the modern syntax and the preprocessor takes care of it all.

What about non-supporting browsers? {#ac1}
-----------------------------------

Well, the general strategy has been "let's ignore them" or "hack your fix with javaScript". So I'll be writing a series of articles and demos showing possible fallbacks for common `flexbox` use cases. This is the first one.

Sticky footer fallback tactics for `flexbox` {#ac2}
--------------------------------------------

It's the classic case where the content is so small that the footer is no where near the actual footer of the window. In the past, if you knew the height of the footer and if it stayed the same there was a [smart trick](http://ryanfait.com/resources/footer-stick-to-bottom-of-page/), sadly the trick is scarcely relevant now.

### We don't know the footer's height. {#ac3}

In modern day *responsive* world you never know the `height` of your footer. Even if you don't build *responsive* layouts, if you use a CMS, the odds are that the footer content is editable by the site owner, so you'll never be certain of the height.

Here [flexbox comes to the rescue](http://philipwalton.github.io/solved-by-flexbox/demos/sticky-footer/) . But what to do in **browsers without flexbox**? or where to fall back?

**Here are some options showing possible fallbacks** <http://baamenabar.github.io/real-world-flexbox/sticky-footer.html> .

![](img/content/flexfall.png):http://baamenabar.github.io/real-world-flexbox/sticky-footer.html

In all CSS solutions we need for the document container and the body to fill all the available height of the window.

bc(language-css).\
html, body{\
height:100%;\
}

Fallback Options
----------------

### 1. Do nothing {#ac4}

That won't break

### 2. JavaScript Layout {#ac5}

Measure the footer with JavaScript and position it accordingly. Though I love JavaScript, I don't like using it merely for presentational purposes, specially on older browsers where resources and JS engines are less than ideal.

### 3. Go beyond the fold {#ac6}

You will always know the minimum height the footer can have. The least we can do is push it to the bottom and show at least that much. We will borrow code from [Ryan Fait\`s Sticky Footer technique](http://ryanfait.com/resources/footer-stick-to-bottom-of-page/) .

![](img/content/flexfall-beyond.png)

bc(language-css).\
.global-wrapper{\
min-height:100%;\
margin-bottom: -50px;//this is the minimal height\
&:after{\
content: ' ';\
display: block;\
height: 50px;\
}\
}

This looks like the content is bigger than the window, which, in some cases, is better than the "too little content" layout.

If you want to use flexbox with this fallback you need modernizr, or the fallback will overwrite the flexbox properties.

bc(language-css).\
.global-wrapper{\
min-height:100%;\
margin-bottom: -50px;\
&:after{\
content: ' ';\
display: block;\
height: 50px;\
}\
}\
.the-footer{\
flex-shrink: 0;\
}\
.flexbox, .flexboxlegacy {\
body{\
display: flex;\
flex-direction: column;\
}\
.global-wrapper{\
flex: 1 0 auto;\
min-height: 0;\
margin-bottom: 0;\
&:after{\
content: none;\
}\
}\
}

### 4. "Extend" the footer background color. {#ac7}

![](img/content/flexfall-extend.png)

bc(language-css).\
body{\
background: \#333;\
.global-wrapper{\
background-color: white;\
}\
}

In truth what happens here is that the background of the body is the same of the footer, and the wrapper `.global-wrapper` that holds the rest of the content has the actual background color for the content.

This option is my favourite as it doesn't need modernizr.

More like this
--------------

I'll be soon tackling other `flexbox` use cases and finding proper fallbacks for each.
