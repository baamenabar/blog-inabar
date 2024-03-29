title:Github pages gh-pages branch from a sub folder\
subtitle:When your root folder is not what you want as the root of your Github Pages root directory.\
classes: language-markup\
pubDate:2016-03-11T17:48:55+00:00

<header>
<hgroup>
Github pages gh-pages branch from a sub folder
==============================================

Alternatives to have the build output directory be the project's page.
----------------------------------------------------------------------

</hgroup>
</header>
Currently I'm working with a static site generator, and it builds to a sub-folder. I want this subfolder to be the one served as the project page.

So far I've found a couple decent solutions:

1.  [Set gh-pages as an orphan branch on the build folder](#ac1)
2.  [Do a subtree push to the gh-pages branch](#ac2)
3.  [Build and deploy with https://github.com/X1011/git-directory-deploy](#ac3)

Let's say we have a repo called `my-project` inside we have the folder `build/dist` and that's the one you want to have as the root of the `gh-pages` site.

Orphan branch {#ac1}
-------------

Let's start maybe with the most intuitive one, at least for me. This I had done before, it has proven pretty flexible specially for Styleguide pages, it works but is a bit of a hasstle to setup, and each time you have to add, commit and push separately from the main branch.

First, if you don't already have it on the `.gitignore` file you have to add the `./build/dist` path to it. In case you need to, also remove it from the repo with.

git rm -r -f --cached build/dist

Then you `cd build/dist` cd into your folder, and clone the `my-project` repo inside this folder. We create an orphan branch (Without parent or history).

git checkout --orphan gh-pages

We remove all content from the branch.

git rm -rf

Now you are ready to run your build script, after you have some files there, you add, commit and push them to origin. And each time after you build.

git add -A\
git commit -m "Some message"\
git push origin gh-pages

Althought it could be scripted to be part of the deploy step, it is a bit cumbersome and is probably why I had forgotten about this solution. That's why I went looking again for a good solution and found the following two.

Subtree push {#ac2}
------------

This is more elegant and much more straight forward.

You have a regular repo, the `build/dist` folder must **not** be ignored. After building and commiting your branch you do the magic trick.

git subtree push --prefix build/dist origin gh-pages

Done, you'll see your build folder deployed by Github briefly.

Git Directory deploy {#ac3}
--------------------

This is by far the most hard-core option, but it requires that you are developing in a linux environment, or somewhere you can run shell scripts.

<https://github.com/X1011/git-directory-deploy>

As I'm constantly jumping between a mac, a Linux and a Windows machine and I never know in which one I'll be developing in, I rather not rely on shell scripts. But I read the code and it is very clever, I will use it for sure when I have somthing bigger to deploy.

Conclusion
----------

I'll be using subtree push the most, it's the most straight forward option of them all.
