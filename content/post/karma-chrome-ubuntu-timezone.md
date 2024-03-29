title: WSL Ubuntu has no timezone set, making Chrome fail on Intl related methods, when running tests with Karma.\
classes: language-markup\
pubDate:2018-04-18T21:33:02+02:00

WSL Ubuntu has no timezone set, making Chrome fail on Intl related methods, when running tests with Karma.
==========================================================================================================

While testing an Angular CLI app, from my Windows Subsystem Linux, I got a weird error. But first of all I need to setup Google Chrome to run it in headless mode.

To install follow the instructions layed out here: https://askubuntu.com/a/79284/469134

    sudo apt-get install libxss1 libappindicator1 libindicator7
    wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
    sudo dpkg -i google-chrome*.deb

if dpkg fails run the following:

    sudo apt-get install -f

After that run `google-chrome --version` to confirm it´s available.

Karma error
-----------

With karma running, you could spend months without problems, but if your code deals with timezones you might get errors like these:

    Chrome (headless) ERROR
      {
        "message": "Uncaught RangeError: Unsupported time zone specified undefined
        at http://localhost:9876/_karma_webpack_/main.bundle.js:606:57
        RangeError: Unsupported time zone specified undefined
            at Object.DateTimeFormat (native)
            at Object../src/app/core/time/time-zone.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:606:57)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/core/http/index.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:302:10)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/core/index.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:673:10)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/globals/globals.module.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:1650:15)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
       at Object../src/app/globals/index.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:1697:24)",
        "str": "Uncaught RangeError: Unsupported time zone specified undefined
        at http://localhost:9876/_karma_webpack_/main.bundle.js:606:57
        RangeError: Unsupported time zone specified undefined
            at Object.DateTimeFormat (native)
            at Object../src/app/core/time/time-zone.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:606:57)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/core/http/index.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:302:10)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/core/index.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:673:10)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/globals/globals.module.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:1650:15)
            at __webpack_require__ (http://localhost:9876/_karma_webpack_/inline.bundle.js:55:30)
            at Object../src/app/globals/index.ts (http://localhost:9876/_karma_webpack_/main.bundle.js:1697:24)"
      }

The error was triggered by a call to: `Intl.datetimeformat().resolvedoptions().timezone` which at some point is undefined, because the Browser does not seem to have one. In any other environment the same config works fine. So it had to be Ubuntu.

Let´s check the date... there are several way to do that, and you can start with `date`

    Wed Mar 28 14:21:24 DST 2018

but I have no idea what DST stands for, so I tried to get a more explicit one or the offset, here I bumped with a number of options (https://askubuntu.com/a/27599/469134)

    cat /etc/timezone
    grep UTC /etc/default/rcS
    date
    # hardware clock
    sudo hwclock --show

only `date` worked for me...\
and hwclock threw up the following:

    hwclock: Cannot access the Hardware Clock via any known method.
    hwclock: Use the --debug option to see the details of our search for an access method.

![](https://i.imgflip.com/2a6biu.jpg)

Also found a lot of people using `timedatectl` but guess what...

    timedatectl
    Failed to create bus connection: No such file or directory

So it seems that WSL Ubuntu comes without `systemd-services` package and if I try to install it with `apt-get` I get: `Note, selecting 'systemd' instead of 'systemd-services'`.

Finally found the solution at: https://github.com/HowardHinnant/date/issues/102

it is suggested to do the following:

    rm /etc/localtime
    ln -s /usr/share/zoneinfo/Europe/Berlin /etc/localtime

but I ended up doing

    sudo mv /etc/localtime /etc/no-localtime
    ln -s /usr/share/zoneinfo/Europe/Berlin /etc/localtime

And that worked perfectly to set my timezone to CEST (my actual timezone).
