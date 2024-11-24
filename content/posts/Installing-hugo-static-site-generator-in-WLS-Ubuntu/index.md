+++
date = "2019-07-31T20:11:06+02:00"
title = "Installing Hugo static site generator in WLS Ubuntu"
draft = true
tags = ["ssg", "hugo",  "setup", "first-steps"]
+++

# Installing Hugo static site generator in WLS Ubuntu

Use hugo they said, it'll be easy they said.

The recommended methods for installing hugo in Linux Ubuntu are:

-   Snap
-   Homebrew for Linux (Linuxbrew)
-   apt-get
-   Install the binaries

## Snap

do I even have snap? `snap`
... some help output comes from the terminal. Sweet!

```
snap install hugo --channel=extended
```

but...

```
Interacting with snapd is not yet supported on Windows Subsystem for Linux.
This command has been left available for documentation purposes only.
```

...next!

## Homebrew for linux

In my head, homebrew is for macs... next!

## apt-get

Obviously, right?

```
sudo apt-get update
sudo apt-get install hugo
```

Seems ok, does what the manual says. But after installing a recent theme, in this case **inkblotty**, I want to compile and serve: `hugo server -D` and it fails:

```
ERROR: 2019/07/31 22:09:34 template.go:350: template: theme/_default/single.html:13: function "default" not defined
ERROR: 2019/07/31 22:09:34 template.go:350: template: theme/_default/summary.html:19: function "default" not defined
ERROR: 2019/07/31 22:09:34 template.go:350: template: theme/partials/relatedposts.html:4: function "T" not defined
ERROR: 2019/07/31 22:09:34 template.go:350: template: theme/partials/sidebar.html:5: function "T" not defined
ERROR: 2019/07/31 22:09:34 general.go:222: Error while rendering page post/test-post.md: html/template: "theme/_default/single.html" is an incomplete template
```

Searching for the error I get a lot of unrelated errors, but one solution writes about this problem being fixed after version `0.54`. Huh! let`s check which is the version I just installed.

```
Hugo Static Site Generator v0.16-DEV BuildDate: 2016-02-06T18:14:17+01:00
```

**WAT?**

...also check the date...

2016 ... **WAT?**

...next!

## Homebrew for linux or Linuxbrew

Linuxbrew does not sound so bad now, well the name still sounds bad, but _installing_ it does not sound so bad.

...easier said than done. After copy and pasting a suspiciously specific looking set of commands.

Worked more or less until I closed and opened my shell again... then:

```
zsh: command not found: brew
```

...hmmm, then probably only available to bash...

```
bash: command not found: brew
```

...next!

## Install the binaries

In one of the first paragraphs it literally says:

> Just grab a precompiled binary!

so, just got to the [Hugo releases page](https://github.com/gohugoio/hugo/releases) and copied the link for the Linux 64bit tar (My Ubuntu is 64bit). Got it into my projects folder, checked the contents and deflated it, check that it works.

```
wget https://github.com/gohugoio/hugo/releases/download/v0.56.3/hugo_0.56.3_Linux-64bit.tar.gz
tar tvf hugo_0.56.3_Linux-64bit.tar.gz
tar -xvzf  hugo_0.56.3_Linux-64bit.tar.gz
./hugo version
```

```
Hugo Static Site Generator v0.56.3-F637A1EA linux/amd64 BuildDate: 2019-07-31T12:51:09Z
```

finally move it to the `/usr/local/bin` folder, where it is automatically in your `PATH`.

```
sudo mv ./hugo /usr/local/bin/hugo
```

now I `cd` into my project and `hugo server -D`

All good and running.

PS: do not forget to clean up the `LICENSE` and the `README.md` from wherever you uncompressed the tar.
