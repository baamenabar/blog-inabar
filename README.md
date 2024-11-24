# blog-inabar
Blog built with Hugo SSG, about web applications, websites and software development in general.

## Local development

First, remember to pull the git _submodule_ for the theme by running `git submodule update`.

You need [Hugo](https://gohugo.io/installation/) installed in your local env. The run the hugo server that will start watching your file system, when you pass `-D` it will include drafts.

```sh
hugo server -D
```

Navigate to http://localhost:1313/
