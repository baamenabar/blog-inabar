title: Setting up Karma tests for an Angular CLI project.\
classes: language-markup\
startDate:2018-04-25T20:11:03+02:00\
pubDate:2018-12-27T14:35:40-08:00

Karma (unit) Tests for an Angular CLI project aimed at CI
=========================================================

Part 1 of the Testing Angular series: How to configure the Jasmine Unit tests run by Karma.
-------------------------------------------------------------------------------------------

With the final goal of having a CI pipeline building and testing an Angular project. We begin with troubleshooting for running an Angular CLI project karma-jasmine tests in Linux environments, including WLS.

What do we want from our unit tests?
------------------------------------

The barest of the barest is code coverage with tests. Ideally your unit tests cover more than just branches, but just by having close to 100% coverage your code will be better.

We need reporting, for us, and later for our pipeline.

We need to decide in a testing environment: a Browser, a Headless Browser, or virtual dom. I really really like the purity of not touching a browser to run the **unit** tests. The default uses Chrome (which is not easy to install on Linux environments), so we'll stick to that, but later I will explore switching to [jsdom](https://www.npmjs.com/package/karma-jsdom-launcher) or even Jest.

### What are unit tests in Angular?

... not going there ... I've seen many kinds. It is up to you.

Configuration
-------------

Most of the config is done in `src/karma.conf.js` and this will be loaded by the `ng test` command.

In theory you can run Angular tests directly using karma, but in practice you will have more trouble and misconfigurations than anything. The command `ng test` can forward some flags to karma, but not all and it will not overwrite many configs in the `src/karma.conf.js`. You can even have custom flags, since the config is a `.js` file that will be run on node, so you can capture the params passed to it.

The dafault file has Chrome as the target browser which works fine if you ar running on a Windows or Mac env, but on linux you most likely have Chromium, on WSL it is the same.

### Installing Chrome to run it Headless (in Linux):

On the Windows Linux Subsystem you can install chrome directly from google.

To install follow the instructions layed out here: https://askubuntu.com/a/79284/469134

    sudo apt-get install libxss1 libappindicator1 libindicator7
    wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
    sudo dpkg -i google-chrome*.deb

if dpkg fails run the following:

    sudo apt-get install -f

After that run `google-chrome --version` to confirm it´s available. Then just run `google-chrome`.

#### Troubleshooting:

The first error you will get by trying to run chrome might be something like:

    [3333:3333:1234/567890.123456:ERROR:browser_dm_token_storage_linux.cc(93)] Error: /etc/machine-id contains 0 characters (32 were expected).

or

    [3333:3333:1234/567890.123456:ERROR:browser_dm_token_storage_linux.cc(93)] Error: /etc/machine-id contains 32 characters (32 were expected).

I have read this is not supposed to happen, and should not be required by the browser, but it is, even by version 71, my guess is it?s trying to get show something on screen or something of that sort, because if you run.

    google-chrome --headless

The error goes away, but now you get something like:

    [1227/230724.702337:ERROR:gpu_process_transport_factory.cc(967)] Lost UI shared context.

Which apparently means too many possible things. So copying and pasting around flags what works at the end of 2018 is:

    google-chrome --headless --disable-gpu --no-sandbox --remote-debugging-port=9222

This means we need to update the `src/karma.conf.js` and tell it to use a custom launcher, in our case a custom configuration for the Chrome Launcher. First we rename the browser sring to something we understand

bc(language-javascript). browsers: \['Chrome'\],

to

bc(language-javascript). browsers: \['ChromeHeadless'\], // or MyChromeHeadlessConfiguration if you want

Then you need to add the customLaunchers (just below browsers is fine)

bc(language-javascript).\
browsers: \['ChromeHeadless'\],\
customLaunchers: {\
ChromeHeadless: {\
base: 'Chrome',\
displayName: 'Chrome (headless)',\
flags: \[\
'--headless',\
'--disable-gpu',\
'--no-sandbox',\
'--remote-debugging-port=9222',\
\]\
}\
}

Code coverage
-------------

The defaults for now are fine, I just like to add `text-summary` to the lists of reporters. So my istambul reporter config looks like this.

bc(language-javascript). coverageIstanbulReporter: {\
dir: require('path').join(\_\_dirname, '../coverage'),\
reports: \['html', 'lcovonly', 'text-summary'\],\
fixWebpackSourcePaths: true,\
},

Some things you don´t have to test, like mocks, to prevent them from ruining your coverage use:

    /* istanbul ignore next */

for istambul to ignore the next line

Wrapping up
-----------

Now just run your tests `ng test --code-coverage`.
