title:SVG basic markup and syntax\
subtitle: I´m always trying to figure out the basic SVG markup. I'm saving this here with the things I learn about SVGs\
classes: language-markup\
pubDate:2018-03-21T18:28:47-02:00

<header>
<hgroup>
SVG basic markup and syntax
===========================

I´m always trying to figure out the basic SVG markup.
-----------------------------------------------------

</hgroup>
</header>
I'm saving this here with the things I learn about SVGs. An SVG needs some basic markup, like the `<svg>` tag, some other stuff is optional, depending on the use.

    <?xml version="1.0" encoding="utf-8"?>
    <svg id="MDN_Web_Docs" data-name="MDN Web Docs" viewBox="0 0 219 106" preserveAspectRatio="xMaxYMid" width="219px" height="106px" xmlns="http://www.w3.org/2000/svg">
        <defs>
            <style>.cls-1{fill:#fff;}</style>
            <symbol id="icon--home" viewBox="0 0 20 20">
                <title>home</title>
                <path d="M19.871 12.165l-8.829-9.758c-0.274-0.303-0.644-0.47-1.042-0.47-0 0 0 0 0 0-0.397 0.3-0.141s0.218 0.050 0.3 0.141l5.7 6.3v9.123z"></path>
            </symbol>
        </defs>
        <title>MDN Web Docs</title>
        <path d="M49.752 30.824h59.917v17.132H49.75zm57.23 12.3c-.103.03-.21.048-.316.05-.338 0-.514-.15-.514-.582v-3.256c0-1.71-1.352-2.543-2.976-2.543-1.107-.025-2.205.207-1.88.634-2.88z" fill="#FFF"/>
    </svg>

this is a simple version of the mozilla logo plus a symbol from an icon set.
