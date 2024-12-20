---
title: Picture capture and uploader app with ST2
summary: Building a hybrid app for taking photos and uploading the files when wi-fi is available.
classes: language-javascript
pubDate: 2012-10-03 11:40:54
---

# Upload camera pictures with Sencha Touch 2

## Using Apache Cordova (Phonegap) and ST2 to compile a native Android image uploader

### ...and some troubleshooting for other common problems

*Published* / Publicado : <time datetime="2012-10-03 11:40:54">2012-10-03 11:40:54</time> | *By*: Agustín Amenabar L.

For an app we are building, came the need for taking pictures as part of a industrial process log, these pictures are supposed to be uploaded with the rest of the log, but as connections are not reliable and data plans have caps, we are uploading only when there's wi-fi available.

This is being developed in Sencha Touch 2 (ST2) and I'll be making a proof of concept app to test and debug all the problems I'm going to have, without messing up the rest of the big application.

Here is the final app folder with server-side code in a github repo: <https://github.com/baamenabar/MIUp-sencha-app-/>

Let's create the app from the sencha dir: <kbd>sencha generate app MIUp ../miup</kbd> this created a folder `miup` (**MIUp** stands for Mobile Image Upload) with an empty ST2 app in my localhost folder.

We are going to combine two main pieces of code; the Camera controller from the [sencha docs](http://docs.sencha.com/touch/2-0/#!/guide/native_apis) and the **FileTransfer** Object from the [Apache Cordova docs](http://docs.phonegap.com/en/2.1.0/cordova_file_file.md.html#FileTransfer) to upload the pictures.

## Setting up Sencha {#hid_2}

But first we must set a button with an action to open the camera, so we change the contents of the Main view (<kbd>app/views/Main.js</kbd>) to:

```js
Ext.define("MIUp.view.Main", {
extend: 'Ext.Panel',
requires: [
'Ext.TitleBar',
'Ext.Button',
'Ext.Label'
],
xtype: 'mainviewport',
config: {
fullscreen:true,
cls: 'snapp',
styleHtmlContent: true,
scrollable: true,
items: [
{
docked: 'top',
xtype: 'titlebar',
title: 'Camera Upload Demo'
},
{
xtype:'label',
html:"

<h1>
There's a whole world out there

</h1>
"
},
{
xtype:'button',
html:'

<h1 style="color:#FFF">
SNAP!

</h1>
',
action:'snapPicture',
ui:'action rounded',
height: 100,
style:'margin-bottom:1em;'
},
{
xtype:'label',
html:"

<h1>
a piece of it.

</h1>
"
}
]
}
});
```

If we test the app now there's some things we must address, a few lines of CSS to fix some stuff for Chrome and center the HTML we generated here. In the `index.html` , at the end of the `<style>` tag, add the following lines:

```css
.x-desktop .x-title .x-innerhtml{padding: 0;}
.snapp{text-align: center;}
```

then

## Access the camera {#hid_3}

Now we make the controller that listens to the button, opens the camera and listens for the successful capture, then stores the file *uri* in a store.

```js
Ext.define('MIUp.controller.General',{
extend: 'Ext.app.Controller',
requires: [
'Ext.device.Camera'
],
config: {
refs: {
principal:'mainviewport',
},
control:{
'mainviewport button[action=snapPicture]': {
tap: 'openCamera'
}
}
},
openCamera: function(button,eve){
Ext.device.Camera.capture({
success: this.onCaptureSuccess,
scope: this,
quality : 85,//for testing havving this at 50 does faster uploads
source: 'camera',
destination: 'file'
});
},
onCaptureSuccess: function(uri) {
console.log('got foto:'+uri);
//Ext.getStore('theImageQueue').add({src: uri});
}
});
```

Remember to add `controllers: ['General'],` in the app.js so the app includes the controller.

Now, if we test this, when you click the **<span class="button">SNAP!</span>** button the console should show something like: <samp> got foto:http://www.sencha.com/img/sencha-large.png </samp>. At this point we still don't need Cordova to compile a "native" app, with the *Sencha SDK Tools* you'll compile fine. But from now on, compiling with Cordova is the only way to get persistent localstorage and to upload the files to a server.

## Storing the pictures locally {#hid_4}

So far so good, we are taking pictures with the app, no biggie. Up next is the storing of the list of pictures we will upload, and that we will do with a *localstorage* Store we will create now, we will not define a Model in a separate file for such a simple Store.

```js
Ext.define('MIUp.store.ImageQueue',{
extend: 'Ext.data.Store',
xtype:'imagesqueue',
requires:['Ext.data.proxy.LocalStorage'],

config: {
fields:['timestamp','src'],
storeId:'theImageQueue',
autoLoad:true,
proxy:{
type:'localstorage',
id:'idImagesQueue',
reader: {
type: 'json'
}
}
}
});
```

This could be even simpler, but it's always nice to have some room to store extra stuff without further complications.

Once again we must add this component definition to the app.js so it loads the store: `stores:['ImageQueue'],`. If we tested here the localstorage would be created, but would remain empty, the controller we must make a modification to the controller file, the *onCaptureSuccess* function we need to change as follows.

```js
onCaptureSuccess: function(uri) {
console.log('got foto:'+uri);
var lostor = Ext.getStore('theImageQueue');
lostor.add({
src: uri,
timestamp: new Date().getTime()
});
lostor.sync();
}
```

Here we are adding an object with two pieces of data to the store, then `.sync()` makes it persistent saving them as localstorage, we can check that in the webInspector.

![](img/content/st2-miup-01.png)

## Detecting data connection {#hid_5}

Sencha Touch 2 provides a very easy way of knowing if you are connected or not, what kind of connection you are on, and even detecting network changes.

At the end of the `General.js` controller we will add a listener for the app's initialization, in there we'll register a listener for the [**onlinechange**](http://docs.sencha.com/touch/2-0/#!/api/Ext.device.Connection-event-onlinechange) event.

```js
launch:function(){
console.log('This thing has started.');
Ext.device.Connection.on({
onlinechange: function( online, type, eOpts ){
if(online){
if(type  Ext.device.Connection.WIFI || type  Ext.device.Connection.ETHERNET){
Ext.Msg.alert('Connection', 'We are now connected with: '+type);
}
}
}
});
}
```

Remember to add `'Ext.device.Connection'` to the *requires* array at the beginning of this controller. For the time being we'll just show an alert on connection to wifi.

### Opening Cordova (Phonegap)

This is half working, Sencha detects the connection change, but doesn't know what kind of connection there is, so, now we turn to *Cordova* to get the rest of the stuff we need working. Here is an excellent article on working with both [Packaging a Sencha Touch 2 application with PhoneGap for Android](http://andidog.de/blog/2012/06/packaging-a-sencha-touch-2-application-with-phonegap-for-android/) . **Beware! adding Cordova we are opening a new can of worms.** Yes, Cordova will help us with many device APIs but Ii will be hard or even impossible to access some of Sencha's device APIs, and with `Connection` we have a very good example.

#### With Sencha SDK Tools

```js
    Ext.device.Connection.on({onlinechange:function()});//works like a charm
    Ext.device.Connection.getType();//doesn't work
    Ext.device.Connection.isOnline();//works
```

#### With Cordova

```js
    Ext.device.Connection.on({onlinechange:function()});//doesn't work
    navigator.network.connection.type;//works
    Ext.device.Connection.isOnline();//works
```

So some things work with some, some other don't, and some work with all... that's enough for me.

Note that for Cordova to have access to the ST2 device APIs after you build you *must* use <kbd>sencha app build package</kbd> (that took me about 2 hours to figure out).

If we are going to use Cordova there's yet another issue to address. Sencha and Cordova load in an unreliable order, so, when you have Cordova's `"deviceready"` event firing Sencha might not be ready (which would be fine), but sometimes, when Sencha's `launch` function fires, Cordova is not ready, so you can't rely on it's device APIs to be working... big mess, one that James Pearce solved in his [A Sencha Touch MVC application with PhoneGap](http://www.sencha.com/learn/a-sencha-touch-mvc-application-with-phonegap/) tutorial, in the **Instantiating the Application** section.

But I'm sure anyone reading this has dealt with this pattern before; the solution is the same old solution and we'll do the same here.

### Back to the App

In the `app.js` file we will add the following functions to handle Codrova's device APIs, like: check connection, if frameworks are loaded, and file upload.

```js
var oneReady = false;//this is for checking who loaded first

function checkConnection() {
if(!navigator.network)return 'UNKNOWN';
var networkState = navigator.network.connection.type;

var states = {};
states[Connection.UNKNOWN] = 'UNKNOWN';
states[Connection.ETHERNET] = 'ETHERNET';
states[Connection.WIFI] = 'WIFI';
states[Connection.CELL_2G] = 'CELL_2G';
states[Connection.CELL_3G] = 'CELL_3G';
states[Connection.CELL_4G] = 'CELL_4G';
states[Connection.NONE] = 'NONE';

return states[networkState];
}

/***** CORDOVA IMAGE UPLOAD CODE ****/

function uploadPhoto(imageURI,ts) {
var options = new FileUploadOptions();
options.fileKey="files";//Notice that this should be tha same param value that the server should be expecting
options.fileName=imageURI.substr(imageURI.lastIndexOf('/')+1);
options.mimeType="image/jpeg";

var params = new Object();
params.timestamp = ts;//this is just data passed by me to be stores with the file.

options.params = params;

var ft = new FileTransfer();
ft.upload(imageURI, encodeURI("<http://m0.cl/t/miup/index.php>"), win, fail, options);
}

function win® {
console.log("Upload Code = " + r.responseCode);
console.log("Upload Response = " + r.response);
console.log("Upload Sent = " + r.bytesSent);
MIUp.app.getController('General').oneImageSuccess(r.response);
}

function fail(error) {
alert("An error has occurred: Code = " + error.code);
console.log("Upload upload error source " + error.source);
console.log("Upload upload error target " + error.target);
MIUp.app.getController('General').oneImageFail(error.source);
}

/**** END OF CORDOVA IMG UPLOAD***/

function onDeviceReady() {//this is for checking if sencha has already started when cordova is ready
console.log('Cordova ta´ ready');
if(MIUp.app.oneReady){
MIUp.app.getController('General').hola();
}else{
MIUp.app.oneReady = true;
}
}

document.addEventListener("deviceready", onDeviceReady, false);
```

This was taken directly from the Cordova documentation, I've just added and modified some parts to fit this app. Note that this functions will be available globally.

As we are working with Cordova, in this stage the easy way to debug is with eclipse connected via USB to the phone, and it's better to build using <kbd> sencha app build testing </kbd>, just make sure the build path is properly configured in the `app.json` file. Even still I did add a label to the view to help me debug; so in the `Main.js` file after the last label we add:\
```js
,{
xtype:'label',
id:'hconsole',
html:'log:'
}
```

With that ready, lets get working on the controller, we have yet a lot to do:

- Build a method to start the app with both sencha and cordova frameworks loaded
- Build a method to log stuff into the new label as if it was a console.
- Build the image queue upload / fail cycle.

First the function to get things started.

```js
    hola:function(){//this is our true launch function
            MIUp.bothReady=true;
            MIUp.semiConsole = Ext.getCmp('hconsole');//this is an “alias” for our log label defined in Main.js
            this.connectionPoll();
            setInterval(this.connectionPoll,30000);
        },
```

As we don't have an event firing when the connection changes, let's just do a poll to check for connection. For the purposes I'm later using this, 30 seconds is a good interval.

```js
    connectionPoll: function(){
            var este = MIUp.app.getController('General');
            MIUp.connectionType = checkConnection();
            este.addLog('...polling... '+MIUp.connectionType+' with store count:'+Ext.getStore('theImageQueue').getRange().length);
            if( MIUp.connectionType=='WIFI' || MIUp.connectionType=='ETHERNET' ){//if we have wi-fi or ethernet
                este.addLog('we have WIFI');
                if(!este.getAreImagesUploading()){//and there aren't any images uploading already
                    if(Ext.getStore('theImageQueue').getRange().length){//and finally IF there are images to upload
                        este.addLog('there is stuff in the queue');
                        /********* WE BEGIN A NEW UPLOAD CYCLE OF THE IMAGES ON THE STORE ********/
                        //Ext.Msg.alert('Begin', 'The file queue will start to upload.');
                        este.uploadNextImage();
                    }
                }
            }
        },
```

This will check for connection, the if there is a queue already uploading and finally if there is anything to upload, only then it will initiate an upload cycle. Also we need to build the `addLog` function.

```js
    addLog: function(toAdd){
            var msgta = MIUp.semiConsole.getHtml()+'<br>'+toAdd;
            MIUp.semiConsole.setHtml(msgta);
            console.log( msgta );
        },
```

I added also console log in there, to use only one function for debugging. We still need the launch function to check who loaded first and wait if Phonegap hasn't.

```js
    aunch:function(){
            console.log('This thing has started.');
            if(oneReady || Ext.os.is.Desktop){//if we are on desktop we assume there's no phonegap.
                this.hola();
            }else{
                oneReady=true;
            }
        }
```

And at last the *upload cycle*.

```js
oneImageSuccess:function(){
var jso = Ext.JSON.decode(response);
if (jso) response = '<a href="'+jso[0].url+'">'*jso[0].url*'</a>';
this.addLog('Uploaded file:'+response);
var imstor = Ext.getStore('theImageQueue');
imstor.removeAt(0);
imstor.sync();
this.uploadNextImage();
},
oneImageFail:function(msg){
this.addLog('We have failure:'+msg);
var imstor = Ext.getStore('theImageQueue');
var oneImg = imstor.getAt(0);
var failedTimes = Number(oneImg.get('fails'))+1;
imstor.add(oneImg);
imstor.removeAt(0);
imstor.last().set('fails',failedTimes);
imstor.sync();// I could do a messier job than the above, but javascript still behaves mysteriously for me.
this.uploadNextImage();
},
uploadNextImage: function(){
this.addLog('starting to upload next');
var imstor = Ext.getStore('theImageQueue');
if (!imstor.getRange().length){
this.setAreImagesUploading( false );
Ext.Msg.alert('Done', 'The file queue is empty.');
return;
}
var oneImg = imstor.getAt(0);
if(oneImg.get('fails')&gt;2){//if this image has failed more than 3 times: delete from queue
imstor.removeAt(0);
imstor.sync();
if (!imstor.getRange().length)return;
oneImg = imstor.getAt(0);
}
this.setAreImagesUploading( oneImg.get('timestamp') );
uploadPhoto( oneImg.get('src'), oneImg.get('timestamp') );
},
```

That's it for the front end, we just need an upload handler on the server side. We will not be checking if the uploads where correct, or if the server managed everything correctly, that is beyond the scope of this tutorial.

## Server side

We'll be receiving the posted files with php. The script basically checks if the file is an image, uploads to a folder and returns a JSON object with information about the uploaded file(s). It is a hacked short version of [BlueImps's Upload Class](https://github.com/blueimp/jQuery-File-Upload/blob/master/server/php/upload.class.php) by *yours truly*; also added a few files to keep it simple, like a listing for the images uploaded at: <http://m0.cl/t/miup/imgs/> For the code we used check <https://github.com/baamenabar/MIUp-sencha-app-/tree/master/upload> .

This took me about 3 days to build, debug and document, I'm hoping it will help someone, at least for debugging.

Let me know how it works for you, and in what you are using this. For questions regarding sencha You'll have better luck asking in the forums or Stack overflow, but if time let's me I'll he happy to help in questions posted here (if any).

Agustín
