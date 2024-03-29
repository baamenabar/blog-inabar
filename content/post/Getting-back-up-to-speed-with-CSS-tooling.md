title:PostCSS with Gulp: up to speed with CSS tooling\
subtitle: Guide to set up CSS transformations with PostCSS and Gulp, choose plugins and keep working with good 'ol Sass (without loosing too much time).\
classes: language-markup\
startDate:2016-04-03T22:58:24+02:00\
pubDate:2016-04-11T23:01:38+02:00

<header>
<hgroup>
Up to speed with PostCSS + Gulp
===============================

Guide to set up Gulp for doing CSS transformations with PostCSS. Choose plugins and keep working with good 'ol Sass (without loosing too much time).
----------------------------------------------------------------------------------------------------------------------------------------------------

</hgroup>
</header>
PostCSS is easy, they said; you should do it, they said; it's fast, they said; it's the future, they said. For me it was confusing enough that I'm writing this guide to explain the process one must go through to decide which plugins to use and how, specifically for Gulp. Let's ponder different aspects to have in consideration when choosing different plugins needed for a medium sized project.

*<span lang="fr">Mise en place</span>*, a list of expectations {#ac2}
--------------------------------------------------------------

PostCSS can do too many things, better **start with what you know you need** and what you want. So far, for small and medium projects `Gulp + Sass` have floated my boat just fine, but I've seen that in big project's thats far from enough, so we'll make a list of what we need our CSS setup to do. Later I'll explain each of this items.

-   Read and compile Sass
-   Lint Sass
-   Autoprefix standard CSS
-   Minify
-   Generate source-maps for debugging minified CSS

Many of the things on the list are new for me, and I guess postCSS is a good way to start playing with them.

If you don't already have it, we need to install gulp globally and locally as a development dependency. We'll also install right away postCSS.

    npm install -g gulp
    npm install --save-dev gulp gulp-postcss

The Folders {#the-folders .ac#5}
-----------

![](img/content/file-structure-postcss-guide.png)

bc(language-javascript).. var gulp = require('gulp');\
var postcss = require('gulp-postcss');

gulp.task('css', function () {

// Array of transformations to pass into post CSS.\
// This could end up in it's own file if it grows too big.\
var transformations = \[\];

return gulp.src('src/css/\*.css')\
.pipe(postcss(transformations))\
.pipe(gulp.dest('dist/css'));\
});

This works, but it will just copy any `.css` file from `src` to `dist`. Not very useful so far, but very reassuring in the sense that we can clearly see there is no black magic at play here.

With or without Sass? {#ac3}
---------------------

Sass is our baseline, the minimum this thing has to do to be useful, but it was far from straightforward. Every introduction and tutorial speak of postCSS (and it's plugins) as a replacement of Sass, even those claiming it doesn't want to replace Sass. All you find is collections of plugins that put together resemble working with Sass, most notably [precss](https://github.com/jonathantneal/precss) gets recommended a lot.

Then you start realizing **you don't need Sass, you need features from it**, sometimes very few. So, the question is: Which features do you need from Sass? For starting a project I would need:

-   **imports**
-   **variables**
-   **nesting** (just a little)
-   **nesting media-queries** (for organization's sake).

Does `precss` fit the bill? Let's try with the following file:

bc(language-css).. @import 'partials/vars';

.one-box {\
border-bottom: \$border solid \$color-red;

// comment justifying nesting\
p {\
color: \$color-grey;\
}

@media screen and (min-width: 30em) {\
background-color: \#DDD;\
}\
}

Impressive, it almost does it, **almost**: it choked on the double slash comment, but after changing to valid css comment this is the output.

bc(language-css). .one-box {\
border-bottom: 1px solid \#922;\
}\
/\* comment justifying nesting \*/\
.one-box p {\
color: \#888;\
}\
@media screen and (min-width: 30em) {\
.one-box {\
background-color: \#DDD;\
}\
}

Of course there is a way to support **double slash or inline comments** there is a couple of plugins for it, after some testing and reading the best was [postcss-strip-inline-comments](https://github.com/mummybot/postcss-strip-inline-comments) , although this has a dependency on [**postcss-scss**](https://github.com/postcss/postcss-scss) which outlines the underlying issue. PostCSS is for CSS, if we want it to read SCSS we need yet another plugin to set as the syntax interpreter.

    npm install postcss-strip-inline-comments postcss-scss --save-dev

Then just pass it as the interpreter in the options of `postcss` in your processors array.

bc(language-javascript).. var transformations = \[\
require('postcss-strip-inline-comments')\
precss({ /\*options\*/ })\
\];

return gulp.src('src/css/\*.css')\
.pipe(postcss(transformations, {syntax:require('postcss-scss')} ))\
.pipe(gulp.dest('dist/css'));

Yes! we get working valid css code, this took me 3 hours of fiddling with plugins and configuration.

### I don't like where this is going

Right now it's doing what I need it to do now. But this will most certainly change, I will need another feature, and I might not have 2 spare hours to fiddle with my gulp file, but I could learn and have the setup ready and have many recipes for different combination of features, but there are two things bothering me, a lot.

#### *Problem N°1*: I'm writing SCSS on a `.css` file

I need to manually tell my text editor that it should use SCSS syntax, not CSS. Well, actually, later I figured you can have your files have the `.scss` extension, but the imports won't work as you expect them to.

If you have `partials/_titles.css` in the `css` folder.

bc(language-css). @import 'partials/titles'; // Works

It works, but you have to tell your editor every time that it's a `.css` file, or you can simply tell it to use *SCSS syntax* for `.css` and for `.scss`.

If you have `partials/_titles.scss` in the `css` folder.

bc(language-css). `import 'partials/titles'; // ...won't work
`import 'partials/titles.scss'; // ...won't work either\
@import 'partials/\_titles.scss'; // THIS WORKS!

If we mess it, when the task runs, this is the output:

bc.. λ gulp css\
\[00:20:39\] Starting 'css'...\
events.js:85\
throw er; // Unhandled 'error' event\
\^\
Error: ENOENT, open 'C:\\htdocs\\static-recipes\\src\\css\\partials\\titles.scss'\
at Error (native)

It took me a bit, it's not perfect, but I can live with this. I'm sure there's a plugin out there, and I'll have it running in a few minutes. For the output it's a similar story, we need to use `gulp-rename` to change the extension outputted by postCSS and then pass it on... nothing crazy, but it's yet another configuration to fiddle with.

#### *Problem N°2*: I'm depending too much on small projects

With Sass I just need Sass, even if gulp disappeared from npm I can just use sass from the command line and be on my merry way.

With PostCSS on the other hand I'm depending on the following packages:

-   postcss
-   postcss-scss
-   postcss-strip-inline-comments
-   precss (actually a wrapper for many postcss plugins)
    -   postcss-partial-import
    -   postcss-mixins
    -   postcss-advanced-variables
    -   postcss-custom-selectors
    -   postcss-custom-media
    -   postcss-custom-properties
    -   postcss-media-minmax
    -   postcss-color-function
    -   postcss-nesting
    -   postcss-nested
    -   postcss-atroot
    -   postcss-property-lookup
    -   postcss-extend
    -   postcss-selector-matches
    -   postcss-selector-not

Just a week after the [**npm kik and left-pad** outage/issue](http://blog.npmjs.org/post/141577284765/kik-left-pad-and-npm*) I wonder which of the two scenarios below is more likely to happen within 1 year.

A. All the developers working on Sass AND libsass stop working and updating **both** projects\
B. Any of the teams (or single developers) stop working and updating in **any** of the above 19 projects

In theory they would still work, until you do an npm update and one of the libraries changes an API, or you need something from a newer version, but it's not compatible with something else... we've already been there several times, it doesn't take years, just months. But on the other hand, I am way more confident that I can pick up any of those plugins and manage to fix a bug. Not so with Sass.

\*I am aware that the kik/left-pad issue is less likely to happen again as it did.

I must also note that my experience with libsass has not been flawless, there have always been weird issues on different environments. So, for the sake of fairness let's review how this happens with sass.

### With Sass

Whatever the outcome, the purpose of this article is to get a whole postCSS setup working with or without Sass

Install the [gulp-sass](https://github.com/dlmanning/gulp-sass) plugin and add it to the task.

    npm install gulp-sass --save-dev

We should rename the files to `.scss` and after postcss just rename them.

bc(language-javascript).. var sass = require('gulp-sass');

gulp.task('sass', function () {\
var transformations = \[\];

return gulp.src('src/css/\*.scss')\
.pipe(sass({outputStyle: 'compressed'}).on('error', sass.logError))\
.pipe(postcss(transformations))\
.pipe(gulp.dest('dist/css'));\
});

The first time running the task all runs fine, and even faster! It took me 3 minutes to setup. So far **Sass is kicking PostCSS's butt**, but, let's give PostCSS a chance.

![](img/content/postcss-css-vs-sass-output.gif)

The speed difference is probably because node modules take a bit more to bootstrap than the compiled libsass library, the processing itself is probably happening in microseconds on both cases. A true speed test would come on really big projects, we'll test that later.

We will keep adding plugins for the rest of the requirements using Sass and the emulators and see how it goes, we will have 2 tasks (`css` and `sass`) with output to 2 different folders. We'll need the gulp-rename plugin for the postCSS only task.

Let's continue ... Linting SCSS {#ac6}
-------------------------------

Code style is important on teams, because it's sort of a meta-language, it implicitly informs you about what you are reading. Code linting is important because it will catch those errors that are crazy complicated to debug later. It also gives your team a sense of identity, it's funny how you can instantly notice code written by external people.

Personally I could write and read spaceless code forever; inlined for..in if..else is fine by me; I don't give a damn about spaces or tabs, or if you put a semicolon when not sure if you need it or not. Although I do enjoy consistent looking code, I also enjoy not stressing over form when function is fine, nice but not a must.

Having said that, I know the value of coherent code style and I am very often discussing these details with the rest of the devs in the company.

There is a lot of talk of a plugin called **Stylelint**. Let's start with installing it and adding it to our tasks.

    npm install stylelint --save-dev

For the Sass task we will use PostCSS before Sass and after Sass using 2 arrays of transformers. The funny thing is that we are going to use <strong>post</strong>CSS to do some CSS <strong>pre</strong>processing. I insist, the whole project should be called **transformCSS**.

Stylelint has a default config `stylelint-config-standard` but it's a whole friggin npm module! With a little digging in Stylelint docs I find a universe of posible configurations; it is a very fancy project, worth reading through the docs. In short, setting up the linter is subject for a whole other article. For now we'll just use an inline object. So far our transformation arrays look more or less like this (assumming you required `stylelint`).

bc(language-javascript).. // we define this object here because we are using it in 2 tasks\
var stylelintRules = {\
[rules]() {\
[color-no-invalid-hex]() true,\
[declaration-colon-space-after]() "always",\
[max-empty-lines]() 2,\
[indentation]() \[4, {\
[severity]() "warning",\
[except]() \["value"\]\
}\]\
}\
};

// css task\
var transformations = \[\
stylelint(stylelintRules),\
require('postcss-strip-inline-comments'),\
precss({ /\*options\*/ })\
\];

// sass task\
var preTransformations = \[\
stylelint(stylelintRules)\
\];\
var postTransformations = \[\];

I run the tasks... and behold![](... a linting warning)

![there was a tab instead of spaces in the indentation](img/content/postcss-first-stylelint-output.png "there was a tab instead of spaces in the indentation")

Stylelint is fantastic, it has all that `scss-lint` didn't have and made me not use it. But Stylelint would not exist if it wasn't for PostCSS. **+1 points for PostCSS!**

One thing, it is potentially messy to have the config object in your gulpfile, unless you really have 4 or 5 rules. It's better on it's own `.stylelintrc` file, ideally in JS or YAML because they supports comments. Having comments in config files avoid going back to settled style discussions.

The details of setting that up that I'll leave for the [docs](http://stylelint.io/user-guide/configuration/) . One last note before I forget it, there is a [Sublime Text 3 plugin for running Stylelint](https://github.com/kungfusheep/SublimeLinter-contrib-stylelint) while you are writing, so you get instant feedback on ST3.

Autoprefixing CSS {#ac7}
-----------------

This is the source of all the postCSS project, if it wasn't for Autoprefixer was the original project from where postCSS emerged.

    npm install autoprefixer --save-dev

We need to define which browsers we want to support, people usually use 2 versions back, but that is overly optimistic, the iOS and Safari update rate is fast, but there's always a big chunk of users that take a while to update (myself included). You can use several metrics to select which browsers to support, and that a whole deal, but if you want to get a good sense of what are you supporting, autoprefixer exposes an info() method.

A good way to test if autoprefixer is working is adding `display:flex;` to an element.

This is how the configs are looking so far, asuming you have the Stylelint on `.stylelintrc` and required `var autoprefixer = require('autoprefixer');`.

bc(language-javascript).. var autoprefixerConfig = { browsers: \['&gt; 1%', 'last 3 versions', 'Android &gt;= 4', 'iOS &gt;= 7'\] };

// This will output what browsers are considered and what properties will be preffixed\
console.log(autoprefixer(autoprefixerConfig).info());

// css task\
var transformations = \[\
stylelint(stylelintRules),\
require('postcss-strip-inline-comments'),\
precss({}),\
autoprefixer(autoprefixerConfig)\
\];

// sass task\
var preTransformations = \[\
stylelint(stylelintRules)\
\];\
var postTransformations = \[\
autoprefixer(autoprefixerConfig)\
\];

If you run the gulp css or sass tasks you'll get a full report of what autoprefixer will do, and what it is supporting.

![](img/content/postcss-autoprefixer-output.png)

Even with this long list of supported browsers we get only 88% of the users. So here we see yet another reason for always develop with progressive enhancements.

CSS Minification {#ac8}
----------------

Sass does it out of the box, although it is said that [cssnano](http://cssnano.co/usage/) is unbeatable at minifying CSS, exactly because it using PostCSS, it can do some fancy optimizations that Sass doesn't do.

    npm install cssnano --save-dev

And we just add it with it's default settings to our transforms arrays, assuming you have added `var cssnano = require('cssnano');`.

bc(language-javascript).. var nanoConfig = {\
safe:false,\
autoprefixer: false\
};

// css task\
var transformations = \[\
stylelint(stylelintRules),\
require('postcss-strip-inline-comments'),\
precss({}),\
autoprefixer(autoprefixerConfig),\
cssnano(nanoConfig)\
\];

// sass task\
var preTransformations = \[\
stylelint(stylelintRules)\
\];\
var postTransformations = \[\
autoprefixer(autoprefixerConfig),\
cssnano(nanoConfig)\
\];

In the config we set `autoprefixer` to false, because we are running it as the previous transform. Setting the `safe` attribute to true is a god move if you are running `cssnano` on an existing codebase, because without it the minification transforms could me too aggressive. But in general you should be safe, for new projects it's definitely a good idea to leave it as false, I will.

![css task and sass task running almost at the same speed](img/content/postcss-cssnano-output.png "css task and sass task running almost at the same speed")

Now, for the first time the PostCSS tasks are running faster than Sass, If we extrapolate, it will probably be even more dramatic with a bigger codebase.

Sourcemaps {#ac9}
----------

In most preprocessors you can have a setting for the compiler to write a comment in the CSS that tells you where in the sourcecode is the origin of the chunk of code you are reading.

![comment before a selector showing where it's defined in the source .scss file](img/content/postcss-source-in-css.png "comment before a selector showing where it's defined in the source .scss file")

The thing is, in production or even on staging environments you don't get that luxury, because we need to serve minified code.PostCSS has in it's own docs mention to \*[gulp-sourcemaps](https://github.com/floridoo/gulp-sourcemaps*). In the wiki of gulp-sourcemaps there is a list with [all the supported plugins](https://github.com/floridoo/gulp-sourcemaps/wiki/Plugins-with-gulp-sourcemaps-support#css) and in the list we find all the modules we are using so far.

    npm install gulp-sourcemaps --save-dev

There are two ways of doing sourcemaps:

-   inline (I don't see the benefit over sass comments)
-   external file, we create a *companion* .map file for the css (there are other options, but this is simple enough).

Our gulp tasks are looking like this:

bc(language-javascript).. /\*\*\
\* Common setup for both tasks\
\*/\
var nanoConfig = {\
autoprefixer: false,\
safe: true\
};\
var autoprefixerConfig = { browsers: \['&gt; 1%', 'last 3 versions', 'Android &gt;= 4', 'iOS &gt;= 7'\] };\
// This will output what browsers are considered and what properties will be preffixed\
//console.log(autoprefixer(autoprefixerConfig).info());

### `css` (PostCSS only)

bc(language-javascript).. gulp.task('css', function () {

// Array of transformations to pass into post CSS.\
var transformations = \[\
stylelint(),\
require('postcss-strip-inline-comments'),\
precss({ /\*no options yet\*/ }),\
autoprefixer(autoprefixerConfig),\
cssnano(nanoConfig)\
\];

return gulp.src('src/css/\*.scss')\
.pipe(sourcemaps.init())\
.pipe(postcss(transformations, {syntax:scssSyntax} ))\
.pipe(rename({ extname: '.css' }))\
.pipe(sourcemaps.write('.'))//this path is relative to the outputed css file\
.pipe(gulp.dest('dest/css'));\
});

### `sass` (PostCSS with Sass)

bc(language-javascript).. gulp.task('sass', function () {\
var preTransformations = \[\
stylelint()\
\];\
var postTransformations = \[\
autoprefixer(autoprefixerConfig),\
cssnano(nanoConfig)\
\];

return gulp.src('src/css/\*.scss')\
.pipe(postcss(preTransformations, {syntax:scssSyntax}))\
.pipe(sourcemaps.init())\
.pipe(sass({outputStyle: 'compressed'}).on('error', sass.logError))\
.pipe(postcss(postTransformations))\
.pipe(sourcemaps.write('.'))//this path is relative to the outputed css file\
.pipe(gulp.dest('dest/scss'));\
});

They are more or less about the same amount of code.

![gulp css and sass tasks run smoothly](img/content/postcss-sourcemaps-output.png "gulp css and sass tasks run smoothly")

We run the tasks successfully. The only change to the minified CSS is that we added a comment at the end of the file referencing the mapfile.

bc(language-css). /\*\# sourceMappingURL=main.css.map \*/

And we now have the new `main.css.map` that looks like this:

    {"version":3,"sources":["main.scss"],"names":[],"mappings":"AAGA,sCACI,6BAAwC,AACxC,oBAAc,AAAd,qBAAc,AAAd,oBAAc,AAAd,YAAc,CAUjB,AAPG,0CAEI,UAAmB,CACtB,AACD,mCAAA,sCACI,qBAAuB,CAC1B,CAAA","file":"main.css","sourcesContent":["@import 'partials/vars';\n\n// first comment\n.one-box, .and-second-box + .sibling-box {\n    border-bottom: $border solid $color-red;\n    display: flex;\n    \n    // second comment\n    p {\n        // third comment \n        color: $color-grey;//after inline comment\n    }\n    @media screen and (min-width: 30em) {\n        background-color: #DDD;\n    }\n}\n"],"sourceRoot":"/source/"}

Basically `JSON` file. What else? We are now able to debug directly to the source.

We can go even a step further, edit the code directly and save the changes from the browser, although that has [more setup needed on Chrome](http://www.sitepoint.com/using-source-maps-debug-sass-chrome/) . But it's doable if your dev environment allows it (Docker yes, Vagrant no).

Bonus: `gulp help` {#ac9}
------------------

Somewhat unrelated, but I've been adding this wherever I have to open a gulpfile to remember what tasks where available.

There's this small module that will read your `gulpfile.js` and print out your tasks, direct or indirect and list them.

![](img/content/postcss-help-output.png)

The module is [gulp-task-listing](https://github.com/OverZealous/gulp-task-listing)

    npm install gulp-task-listing --save-dev

After installing, adding it to your `gulpfile` is just a *one-liner*.

bc(language-javascript). // Add a "help" task to output the available tasks\
gulp.task('help', require('gulp-task-listing'));

Now you just run `gulp help` and you'll get a list of tasks. Of course it can get more complicated than that, but I like that it is just a copy-paste.

Conclusions: Who won? {#ac10}
---------------------

### Is PostCSS able to dethrone Sass?

When you have your first kid you ask yourself: *how could you ever take love away from this creature by loving a second or third child?* Well... PostCSS feels like the coming of a second child, it does similar things, but it does them differently. It (postcss) is much younger so it doesn't have the growth of the first-born (sass). The younger one (poscss) can't do many things, it may never do them, but it also does many new things the elder (sass) will never do. So you find within yourself a renewed and greater capacity of love, now for both (sass & postcss)... One day the eldest (sass) will leave and what the now grown younger person will fill somehow much of the space left by the older one. Finally that child (postcss) will also eventually go.

Just be ready for double the amount of diapers (errors) on crappy days (deadlines).

All together {#ac11}
------------

Now all together would be something like this:

### For Sass

    npm install --save-dev gulp gulp-sass gulp-postcss postcss-scss stylelint autoprefixer cssnano gulp-sourcemaps gulp-task-listing gulp-livereload gulp-filter

First we install all, **inluding livereload**. Next, assuming you already have a [.stylelintrc](https://github.com/stylelint/stylelint/blob/master/docs/user-guide/example-config.md) file for stylelint, then, in the `gulpfile.js` paste the following.

bc(language-javascript).. var gulp = require('gulp'),\
sass = require('gulp-sass'),\
postcss = require('gulp-postcss'),\
scssSyntax = require('postcss-scss'),\
stylelint = require('stylelint'),\
autoprefixer = require('autoprefixer'),\
cssnano = require('cssnano'),\
sourcemaps = require('gulp-sourcemaps'),\
filter = require('gulp-filter'),\
livereload = require('gulp-livereload');

//configs

var nanoConfig = {\
safe:false,\
autoprefixer: false\
};\
var autoprefixerConfig = { browsers: \['&gt; 1%', 'last 3 versions', 'Android &gt;= 4', 'iOS &gt;= 7'\] };

// Styles\
gulp.task('sass', function () {

var preTransformations = \[\
stylelint()\
\];\
var postTransformations = \[\
autoprefixer(autoprefixerConfig),\
cssnano(nanoConfig)\
\];

return gulp.src('src/css/\*.scss')\
.pipe(postcss(preTransformations, {syntax:scssSyntax}))\
.pipe(sourcemaps.init())\
.pipe(sass({outputStyle: 'compressed'}).on('error', sass.logError))\
.pipe(postcss(postTransformations))\
.pipe(sourcemaps.write('.'))//this path is relative to the outputed css file\
.pipe(gulp.dest('css'))\
.pipe(filter('****/\*.css'))// this prevents livereload from reloading the whole page because of the sourcemaps @see: https://stackoverflow.com/questions/34273127/gulp-livereload-reloads-the-entire-page-when-only-css-has-changed\
.pipe(livereload());\
});

// Watch\
gulp.task('watch', function() {\
livereload.listen();\
gulp.watch('src/scss/\***/**.scss', \['sass'\]);\
});

// Add a "help" task to output the available tasks\
gulp.task('help', require('gulp-task-listing'));

Credits {#ac4}
-------

To get here I followed several guides and read the install instructions of many packages, most notably:

[Smashing Magazine: An Introduction To PostCSS By Drew Minns](https://www.smashingmagazine.com/2015/12/introduction-to-postcss/) Very introductory, but left me with a lot of questions.

[postCSS-starter repo](https://github.com/drewminns/postCSS-starter/tree/master/gulp)

[Extending Sass with PostCSS by Ashley Nolan](http://ashleynolan.co.uk/blog/extend-sass-with-postcss) A plain and short explanation how you can still use Sass and PostCSS

[JS Jabber episode on postCSS](https://devchat.tv/js-jabber/206-jsj-postcss-with-ben-briggs) Casually this episode came out just as I was writing this article.

[Floss your style sheets with Stylelint](https://benfrain.com/floss-your-style-sheets-with-stylelint/)

[Broserlist configuration docs](https://github.com/ai/browserslist#queries) Very useful for configuring autoprefixer.
