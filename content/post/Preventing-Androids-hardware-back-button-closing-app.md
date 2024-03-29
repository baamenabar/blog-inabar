title: Preventing Android's hardware back button closing a Sencha app\
subtitle: It also applies to any Cordova (Phonegap) app\
classes: language-javascript\
pubDate:2012-10-11 02:09:46

<header>
<hgroup>
Preventing Android's hardware back button closing an app
========================================================

How to avoid closing a Sencha Touch 2 or Phonegap (Cordova) app when someone clicks he back button on an Android device.
------------------------------------------------------------------------------------------------------------------------

</hgroup>
</header>
*Published* / Publicado : <time datetime="2012-10-11 02:09:46">2012-10-11 02:09:46</time> | *By*: Agustín Amenabar L.

The ideal would be to have it working along the in-app **back button**, but sometimes one has time (or budget) only for a quick hack, in my case a confirmation for closing the app before exiting is exactly what was needed.

Sencha Touch 2 has no way of closing the app, so to close the app we must rely on Phonegap, so here is the code... Plainly ripped off and hacked small from [Michael Ritchie](http://thanksmister.com/2012/07/06/handle-android-hardware-back-button-using-phonegap-and-sencha-touch-2/) and [The Cordova Docs](http://docs.phonegap.com/en/2.1.0/cordova_events_events.md.html#backbutton)

### Sencha way

bc..\
launch: function(){\
/\* regular launch stuff \*/

if (Ext.os.is('Android')) {\
document.addEventListener("backbutton", Ext.bind(onBackKeyDown, this), false); // add back button listener

function onBackKeyDown(eve) {\
eve.preventDefault();\
Ext.Msg.confirm("Exit", "¿Seguro que quieres cerrar el app?", function ( answer ) {\
if ( answer == 'yes') {\
navigator.app.exitApp();\
} else {\
//do nothing\
}\
});\
}\
}\
},

In a Sencha Touch 2 app I'd place it in the `app.js` file, but might as well go inside a controller if you know the ropes.

### Only Phonegap

A Cordova-only implementation would be something like this, the notification syntax is nice on Cordova ( [look at the docs here](http://docs.phonegap.com/en/2.1.0/cordova_notification_notification.md.html#notification.confirm) ):

bc..\
function onDeviceReady() {\
if(device.platform == "Android"){\
document.addEventListener("backbutton", onBackKeyDown, false);\
}\
}

function onBackKeyDown() {// Handle the back button\
navigator.notification.confirm(\
'Are you certain you want to close the app?', // message\
function( index ){\
if( index == 1 ){//look at the docs for this part\
navigator.app.exitApp();\
}\
}, // callback to invoke with index of button pressed\
'Exit', // title\
'Yes,No' // buttonLabels\
);\
}

document.addEventListener("deviceready", onDeviceReady, false);

From their docs I'm not overly confident on Phonegap knowing always if a device is an Android, but it would certainly work for the ones that unequivocally are.

And that's all folks, easy peasy.
