title:Getting file md5 checksums in different OSs\
subtitle:Generating md5 file checksums vary from one OS to another, and because I like developing in varying environments, I need solid equivalences.\
classes: language-markup\
pubDate:2017-05-08T08:14:05-02:00

<header>
<hgroup>
Getting file md5 checksums in different OSs
===========================================

Generating md5 file checksums vary from one OS to another, and because I like developing in varying environments, I need solid equivalences.
--------------------------------------------------------------------------------------------------------------------------------------------

</hgroup>
</header>
For cache breaking I like to use MD5 hashes of files. At the Westwing magazine we had some javascript bundles that we change very seldomly, so a timestamp on each build is too aggressive cache break. We also use file checksums for font bundles.

For this we have a bash script that will create the hash from the bundled files, so, if nothing has changes, we avoid unnecessary requests to our users.

We used to have the projects inside Vagrant Boxes, so all scripts run in linux environment, no problem. But as we moved to Docker (we love it) and we do frontend development directly on our machines, some things changed. For instance the md5sum function, in mac OSX it's called just md5 and the output is slightly different. Because of the output an alias for md5sum will not work.

bc(language-sh). \# Linux\
md5sum Gruntfile.js\
7593278019c7726f7271904cd7dda73a Gruntfile.js

1.  mac OSX\
    bc. md5sum Gruntfile.js\
    MD5 (Gruntfile.js) = 7593278019c7726f7271904cd7dda73a
2.  win 10 with Cmder\
    md5sum Gruntfile.js\
    7593278019c7726f7271904cd7dda73a \*Gruntfile.js

To deal with this we need 3 things:\
1. Check the OS is mac\
2. Filter out the hash from the output\
3. Store on a variable

To figure out which system the script is running into we can use `uname`, and with that we can choose syntax. For detecting OS I found the [following script in Stack Overflow](http://stackoverflow.com/questions/3466166/how-to-check-if-running-in-cygwin-mac-or-linux#answer-17072017)

bc.. \#!/usr/bin/env bash\
ENVIRONMENT = "unknown"\
if \[ "\$(uname)"  "Darwin" \]; then
    \# Do something under Mac OS X platform        
    ENVIRONMENT = "mac"
elif \[ "\$(expr substr \$(uname -s) 1 5)"  "Linux" \]; then\
\# Do something under GNU/Linux platform\
ENVIRONMENT = "linux"\
elif \[ "\$(expr substr \$(uname -s) 1 10)" == "MINGW32\_NT" \]; then\
\# Do something under Windows NT platform\
ENVIRONMENT = "win"\
fi

To get the hash we can use regex with `sed`, or even simpler, we use `cut`, which is a little like `String.prototype.split()` in JS or `explode()` in PHP, it. The main important difference, is the delimiter is only one byte character (ASCII only) and it returns a list of fields, not an array, but you can access it's elements with -f using 1 based index. It is not an array but for this it helps to see it that way.

To get just `md5sum` we pipe (@ | `) the output to `cut@ , split on a space character and get the first field.

    md5sum Gruntfile.js | cut -d ' ' -f 1

to store this in a var we to do a [Command Substitution](https://www.gnu.org/software/bash/manual/bashref.html#Command-Substitution) so the output can be stored on a variable.

    GRUNTHASH=$( md5sum Gruntfile.js | cut -d ' ' -f 1 )

Will output: `7593278019c7726f7271904cd7dda73a`

Storing the hash
----------------

Finally we can store it to a file as a constant.

    echo "<?php define(<'HASH_GRUNT', '$GRUNTHASH'); ?>" > app/path/hashes.php;

Putting it all together
-----------------------

We can build a simple function for hashing.

bc.. \#!/bin/bash

get\_md5\_checksum () {\
if \[ "\$(uname)" == "Darwin" \]; then\
THEHASH=(\$(md5 \$1 | cut -d= -f2 | cut -d " " -f2))\
else\
THEHASH=(\$(md5sum \$1 | cut -d ' ' -f 1))\
fi\
}

get\_md5\_checksum Gruntfile.js\
echo \$THEHASH

This will output the hash of the file either on mac, or linux.

Epilogue
--------

This seemingly simple task taught me a whole bunch of things about bash and bash scripts. I feel way more powerful now with this new set of tools.

### How to md5 a file in PowerShell ?

    Get-FileHash <filepath> -Algorithm MD5

Yeah, you can add that to the mix, if you are up to the task.
