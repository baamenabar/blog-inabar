title:Starting a static site with Jekyll\
subtitle: Building a static site with Jekyll from Windows, and all the trouble I encountered.\
classes: language-markup\
startDate: 2013-10-17T12:34:08+00:00\
pubDate:2013-11-10T21:25:29+00:00

<header>
<hgroup>
Starting a static site with Jekyll
==================================

From cero to published in one post, with all the pitfalls I fell into.
----------------------------------------------------------------------

</hgroup>
</header>
I had to make my CV (or *\[lang=fr\]resumé*), the first impulse was Markdown on GitHub... hmmmm ...now I have the excuse to try out Jekyll. The idea is to make my personal site, with it's domain and a small portfolio, run from Jekyll using GitHub pages. Let's get on with it.

*<span lang="fr">Mise en place</span>* (what we'll need) {#ac1}
--------------------------------------------------------

-   **Ruby** on your machine, if you are on Windows [RubyInstaller](http://rubyinstaller.org/) works like a charm, but you must add the ruby `bin` folder to your *Path* environment variable on windows.
-   **DevKit** (Ruby Development Kit) <http://rubyinstaller.org/downloads/> installing [instructions below](#ac3) .
-   **Phyton 2.7.x** Optional, I didn't.

Read which version of ruby is the most adequate for you. 1.9.3 is the stable one at the time of this writing. We'll continue on the command line. I'll be trying to follow [GitHub's documentation](https://help.github.com/articles/using-jekyll-with-pages) on this.

Installing stuff {#ac2}
----------------

With the console navigate to your folder, once there, we start.

gem install bundler

This will install a package manager that will make our lives easier down the line.\
While it's installing, create a pain text file named `Gemfile` on your folder, with the folowing code.

source 'https://rubygems.org'\
gem 'github-pages'

Back to the console type:

bundle install

Error & installing DevKit {#ac3}
-------------------------

If you didn't install DevKit you'll get this error.

bc..\
Gem::InstallError: The 'fast-stemmer' native gem requires installed build tools.

Please update your PATH to include build tools or download the DevKit from 'http://rubyinstaller.org/downloads' and follow the instructions at 'http://github.com/oneclick/rubyinstaller/wiki/Development-Kit'\
An error occurred while installing fast-stemmer (1.0.2), and Bundler cannot continue.\
Make sure that \`gem install fast-stemmer -v '1.0.2'\` succeeds before bundling.

Download DevKit from <http://rubyinstaller.org/downloads/> .

Uncompress it anywhere you want. Add the `/bin` folder to your *Path* environment variable. In the folder where DevKit resides, run the following commands from command line:

ruby dk.rb init\
ruby dk.rb install

Once DevKit is installed, in the *Path* and windows restarted, we run again `bundle install`.

Starting Jekyll {#ac4}
---------------

Now we should have Jekyll installed. The we must make a new project, navigate with the console to where you want to create your project's folder and type:

jekyll new project-folder

New jekyll site installed in \~/project-folder

This sets up a basic site that hasn't been built yet. It has an `index.html` but it's not a proper html file. We must build it. According to the Jekyll site it's just.

cd project-folder\
jekyll serve

That should build the jekyll project in the folder and start the server and we could navigate to http://localhost:4000 and get some basic site. But we get a huge error, actually 2 errors on my Windows machine.

The build and serve errors {#ac5}
--------------------------

The errors would be something like these:

bc..\
C:/Ruby193/lib/ruby/gems/1.9.1/gems/posix-spawn-0.3.6/lib/posix/spawn.rb:162: warning: cannot close fd before spawn ←\[31m Liquid Exception: No such file or directory - /bin/sh in 2013-10-17-test-post.md←\[0m

\[2013-10-17 20:01:22\] INFO WEBrick 1.3.1\
\[2013-10-17 20:01:22\] INFO ruby 1.9.3 (2012-02-16) \[i386-mingw32\]\
\[2013-10-17 20:01:22\] WARN TCPServer Error: Only one usage of each socket address (protocol/network address/port) is normally permitted. - bind(2)

And if you navigate to `http://localhost:4000` you get a Forbidden error.

\[2013-10-17 20:01:29\] ERROR no access permission to \`/' activate.adobe.com - - \[17/Oct/2013:20:01:29 Hora verano Sudam�rica Pac�fico\] "GET / HTTP/1.1" 403 283 - -&gt; /

The First error is because Jekyll is trying to load Pygments.rb which needs Phyton to work. I don't have phyton, so in the `_configs.yml` file, inside the `project-folder` I deactivated pygments.

pygments: false

If you have Pyton and want to use pygments make sure you are using pygments 0.5.0 and Pyton 2.7.x or so it says here: [Jekyll on Windows: Pygments not working](http://stackoverflow.com/questions/17364028/jekyll-on-windows-pygments-not-working)

The TCPServer error is because there's another process or service listening to that port. We must find out which service it is and shut it down. To do that, in the **cmd** enter:

netstat -abo

And it will list all the connections with port, the process ID or PID and the executable filename involved in the connection. This I learnt from an answer to the question [How can you find out which process is listening on a port on Windows?](http://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows#answer-13281291) in Stackoverflow.

From the listing I discovered that it was a ruby service using the port, so I closed the process from the **Task Manager** and everything ran normally.

At some point I started blaming Wamp of all my ailments. But, so far, there hasn't been any conflicts between WEBrick THHPServer and wamp.

The only note regarding servers here is that to properly test the jekyll project, it has to be tested in `localhost:4000` an not through the regular apache server, paths will be all messed up, I'm sure there is a way, but I'll figure that out later.

Start building <small>(Enough with installing things)</small> {#ac6}
-------------------------------------------------------------

So far all I managed is install de default sample site, less than impressive.

### Content first

I'll start by writing the main article in Markdown... Two days later I'm done, but it turns out it's on it's own repo and I want to start with a very very simple one page site. If we run

#### The bare minimum

I have the following files:

.\
├── README.md\
├── cv.md

I know it won't work, but let's try anyway. Run `jekyll build` and it doesn't fail, it copies what is in the folder to a new `_sites` folder. If we run `jekyll serve` it works fine, only that there's no index.html document so it shows a Forbidden code. I'm liking this.

.\
├── README.md\
├── cv.md\
├── index.html\
├── \_sites\
| ├── README.md\
| ├── cv.md\
| ├── index.html

If we add a simple index.html it also get copied to the `_sites` folder and now <http://localhost:4000> has somthing to show.

BUT! the headers are missing the content-type and character encoding and non ASCII characters get garbled, there should be a `"Content-Type:text/html; charset=UTF-8"` http header.

<!-- Seems we need to breakout the config file, create a new _config.yml-->
Quick search on Google and a ruby gem shows up: [Jekyll UTF-8](https://github.com/fitztrev/jekyll-utf8)

gem install jekyll-utf8

Then we must create a folder and file: `_plugins/jekyll-utf8.rb` with the folowing line of code:

require 'jekyll-utf8.rb'

Now we run `jekyll serve` ... Still no. Filed an issue with the developer of the gem, but he sayd it was not for Windows. Oh well I'll have to stick `<meta charset="utf-8">` in the head.

If we try now, it's working, it spits the html we placed in the `index.html`, but that's no fun, I want to write in Markdown and get html output. For that we need the `_layouts` folder and a layout template in it.

### Enter Liquid {#ac7}

The first time I read *“templating engine”* I ran away, only came back after months. But, so far, Liquid has been as friendly as starting with LESS or SASS.

But for a simple one-pager, it's almost more complicated than to actually write the html instead the Markdown. First we need to add a couple of files

.\
├── README.md\
├── cv.md\
├── index.md\
├── \_sites\
| ├── README.md\
| ├── cv.md\
| ├── index.html\
├── \_layouts\
| ├── default.html\
├── \_includes\
| ├── cv.md

`_layouts/deafaults.html` will be a pretty barebones html file

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">

<title>
Some title

</title>
</head>
<body>
{{ content }}

</body>
</html>
We must change the extension of `index.html` to `index.md`. The extension must be changed if you want the included files to be processed as markdown. This file will have very little code also.

------------------------------------------------------------------------

layout: default\
---\
{% include cv.md %}

Now we do `jekyll build`. If you ever get a message like this.

Generating... error: undefined method \`fetch' for [layout:default](String).

It is because you must put a space after the colon. I honestly don't know why the parser can't understand it without the space, my parser doesn't care (for this blog engine).

Now the build works, and we have one page generated with Markdown.

After adding some css the site is presentable.

Publishing with GitHub pages {#ac8}
----------------------------

If you use Git, it's easy. If you have the project in a repo, just create a new branch from the master called `gh-pages` and push to github.

git checkout -b gh-pages\
git push origin gh-pages

That's all, now you just have to visit the GitHub Pages URL for the repository, in my case it's <http://baamenabar.github.io/cv/>

Setting GitHub Pages as a domain {#ac9}
--------------------------------

The official GitHub instructions are here: <https://help.github.com/articles/setting-up-a-custom-domain-with-pages> basically you have to do setup in two places: the repo and in your domain registrar.

### Setup the domain in the repository {#ac10}

Create a file called `CNAME` with your domain in it, in my case it's `iminabar.com`

### Setup the domain in the registrar {#ac11}

We must create a type `A` record with the value `204.232.175.78`. I use dreamhost for my `.com` domains, so here is a screenshot.

![Setting up the A record in Dreamhost](img/content/starting-jekyll-DNS-at-dreamhost.png "Setting up the A record in Dreamhost")

It's odd that it must be an `A` record and not a `CNAME`, actually it being an A record makes sense, what doesn't make sense is that GitHub asks for a file called CNAME and not `A` or `A-record` or something like that in the repo.

That should be all, if I ever encounter mor errors in this process I'll add them to this post.

Hope this helps someone.
