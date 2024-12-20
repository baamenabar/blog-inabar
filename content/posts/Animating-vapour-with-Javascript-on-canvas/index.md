---
title: Animating vapour with JavaScript on canvas
subtitle: Particle animation rendered on the canvas element
classes: language-markup\
date: 2013-11-13T20:44:47+00:00
---

# Animating vapour with JavaScript on canvas

## Particle animation rendered on the canvas element

As my day to day development must include support for IE8 and sometimes even IE7 playing with the recent toys is not possible, unless they degrade gracefully. But with mobile development, the story is different, now I have to replicate a vapour animation done in flash, but in regular HTML/Javascript. Enter `<canvas>`... All my experience with canvas has been just a few shapes a few years ago, it seemed really similar to the ActionScript drawing API and Bitmap Classes, today I'll put that to the test.

## _<span lang="fr">Mise en place</span>_ {#ac1}

-   Get some proper documentation. The best I could find was the WHATWG spec document for `canvas` <http://www.whatwg.org/specs/web-apps/current-work/multipage/the-canvas-element.html>
-   You need a modern browser, with `<canvas>` support, to properly view this article.

## Set up the HTML {#ac2}

Basically nothing, I'm resetting a few things and the JS code we'll do on the same html.

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">

<title>
Canvas particle system for vapour

</title>
<style>
html, body{\
padding:0;\
margin:0;\
}\
body{\
background-color: \#bbb;\
}

</style>
</head>
<body>
<canvas id="vapour">
</canvas>
<script>
</script>
</body>
</html>
```

## Testing the canvas {#ac3}


We must select the canvas and get the `2d` context to work there. We must set the size of the element and we'll add a rectangle to test.

```js 
var canvas = document.getElementById('vapour');\
var cntxt2d = canvas.getContext('2d');\
canvas.width = window.innerWidth;\
canvas.height = window.innerHeight;\
cntxt2d.fillStyle = "rgba(69, 188, 210, 0.8)";\
cntxt2d.fillRect(64,64,256,218);
```

That should give you someting like this:

![A cyan rectangle over gray background](img/particles-first-rect.png 'A cyan rectangle over gray background')

## Our basic particle {#ac4}

As this is going to be vapour it must be a _flufy_ particle, it should be a disc with a radial gradient fill.

To draw a disc in canvas is pretty easy (way easier than writing a series of bezier paths), and a radial gradient is also easier the the CSS syntax. You must first generate a `CanvasGradient` object with the `createRadialGradient` method and then add color stops with addColorStop. The `CanvasGradient` can then be assigned as a `fillStyle` to the context.

The `arc()` method is one of the `CanvasPathMethods` so the context needs to be initiated in the drawing mode with `beginPath()` and ended with `fill()` (if you want to fill the path with the fillStyle).

```js 
var rGrad = cntxt2d.createRadialGradient(64,64,0,64,64,32);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(252, 194, 18, 0)');
cntxt2d.fillStyle = rGrad;
cntxt2d.beginPath();
cntxt2d.arc(64,64,32, 0, Math.PI*2, false);
cntxt2d.fill();
```

<canvas id="first-grad" style="background:#555;max-width:100%"></canvas>
<script>
var canvas = document.getElementById('first-grad');
var cntxt2d = canvas.getContext('2d');
canvas.width = 579;//window.innerWidth;
canvas.height = 300;//window.innerHeight;
cntxt2d.fillStyle = 'rgba(69, 188, 210, 0.8)';
cntxt2d.fillRect(64,64,256,218);
var rGrad = cntxt2d.createRadialGradient(64,64,0,64,64,32);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(252, 194, 18, 0)');
cntxt2d.fillStyle = rGrad;
cntxt2d.beginPath();
cntxt2d.arc(64,64,32, 0, Math.PI*2, false);
cntxt2d.fill();
</script>

Actually I don't need a disc to hold the radial gradient, it could perfectly be a square. But the problem I see right away is that the position of the circles that generate the radial gradient is relative to the canvas, so I shouldn't even need the arc. The problem with doing particles with radialGradients as fillStyle is performance, I did a [little test](http://cdpn.io/sDpld) modifying a [particle emmiter by Jarrod Overson](http://codepen.io/jsoverson/full/chGDt) so the particles where some small gradients rendered as the `fillStyle` and the “little test” just crashed Chrome.

### Pixel manipulation {#ac5}

What if we generate the gradient only once, and store that data and then paste it in as many places as we need? Let's try that.

```js
//this time we'll put the disk at the top.
var rGrad = cntxt2d.createRadialGradient(10,10,0,10,10,10);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(252, 194, 18, 0)');
cntxt2d.fillStyle = rGrad;
cntxt2d.beginPath();
cntxt2d.arc(10,10,10, 0, Math.PI*2, false);
cntxt2d.fill();
var dotImage = cntxt2d.getImageData(0,0,20,20);
cntxt2d.putImageData(dotImage,128,128);
```

<canvas id="second-grad" style="background:#555;max-width:100%"></canvas>
<script>
var canvas = document.getElementById('second-grad');
var cntxt2d = canvas.getContext('2d');
canvas.width = 579;//window.innerWidth;
canvas.height = 300;//window.innerHeight;
cntxt2d.fillStyle = 'rgba(69, 188, 210, 0.8)';
cntxt2d.fillRect(64,64,256,218);
var rGrad = cntxt2d.createRadialGradient(10,10,0,10,10,10);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(252, 194, 18, 0)');
cntxt2d.fillStyle = rGrad;
cntxt2d.beginPath();
cntxt2d.arc(10,10,10, 0, Math.PI*2, false);
cntxt2d.fill();
var dotImage = cntxt2d.getImageData(0,0,20,20);
cntxt2d.putImageData(dotImage,128,128);
</script>

That doesn't look right. How am I going to build a cloud with bitmaps that don't respect the alpha channel? This took me a while to figure out, and it turns out that if you look at the data inside the `ImageData` object the alpha transparency is there, also if we check for the pixels in the canvas where we put the `ImageData` there's also the supposed transparency. The problem is that `PutImageData()` method doesn't blend the pixels with what is already on the canvas, it just replaces them. At stackoverflow [there was a suggestion](http://stackoverflow.com/questions/14110754/transparency-lost-with-getimagedata-html5-2d-context) to use the `drawImage` method. Which can be an image, or a canvas element. It also turns out that `drawImage` is, at least, 10 times faster in most browsers.

So we add an extra canvas, just the size we need for the gradient bitmap and use that as an image to print on the animation canvas.

<canvas id="small-one" style="display:none">
</canvas>

We add the `display:none;` because we only need the data, we don't need it to render. And to the JS code we add:

```js
var dotImage = cntxt2d.getImageData(0,0,20,20);\
cntxt2d.putImageData(dotImage,128,128);\
var smallCanvas = document.getElementById('small-one');\
var bufcntxt = smallCanvas.getContext('2d');\
smallCanvas.width = 20;\
smallCanvas.height = 20;\
bufcntxt.putImageData(dotImage,0,0);\
cntxt2d.drawImage(smallCanvas,172,172);
```

<canvas id="third-grad" style="background:#555;max-width:100%">
</canvas>
<canvas id="small-one" style="display:none">
</canvas>
<script>
var canvas = document.getElementById('third-grad');
var cntxt2d = canvas.getContext('2d');
canvas.width = 579;//window.innerWidth;
canvas.height = 300;//window.innerHeight;
cntxt2d.fillStyle = 'rgba(69, 188, 210, 0.8)';
cntxt2d.fillRect(64,64,256,218);
var rGrad = cntxt2d.createRadialGradient(10,10,0,10,10,10);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(252, 194, 18, 0)');
cntxt2d.fillStyle = rGrad;
cntxt2d.beginPath();
cntxt2d.arc(10,10,10, 0, Math.PI*2, false);
cntxt2d.fill();
var dotImage = cntxt2d.getImageData(0,0,20,20);
cntxt2d.putImageData(dotImage,128,128);
var smallCanvas = document.getElementById('small-one');
var bufcntxt = smallCanvas.getContext('2d');
smallCanvas.width = 20;
smallCanvas.height = 20;
bufcntxt.putImageData(dotImage,0,0);
cntxt2d.drawImage(smallCanvas,172,172);
</script>

Hooray! now let's animate.

## Animation {#ac6}

Particles can be very complex beings needing many properties to describe their behaviour on screen. In some cases they can have weight, friction, acceleration, max-speed, size, opacity and be afected by fields, etc. In this case we are building a very simple particle system, speed is going to be constant and there's not going to be any interaction with the particles.

### `requestAnimationFrame` {#ac7}

Instead of timeouts or intervals we use this method with far better performance. It's simple to use, you just pass one argument, the function you want to be executed on one frame. For backwards compatibility we'll use Erik Möller and Paul Irish's [requestAnimationFrame polyfill that works even with browsers without support](https://gist.github.com/paulirish/1579671) .

To test the method we'll place particles randomly at every frame. For that we need to create `render` function that will update the canvas on every frame. Adding to what we wrote before.

```js
function randRange(min, max) {
return Math.random() * ( max - min ) + min;
}
function render(){
cntxt2d.clearRect(0,0,759,300);
var len = 150;
while(len--){
cntxt2d.drawImage(smallCanvas, randRange(10,549) , randRange(10, 270));
}
window.requestAnimationFrame(render);//this makes this function recursive
}
render();
```

<canvas id="fourth-grad" style="background:#555;max-width:100%"></canvas>
<canvas id="small-two" style="display:none"></canvas>
<script>
// http://paulirish.com/2011/requestanimationframe-for-smart-animating/
// http://my.opera.com/emoller/blog/2011/12/20/requestanimationframe-for-smart-er-animating
// requestAnimationFrame polyfill by Erik Möller. fixes from Paul Irish and Tino Zijdel
// MIT license
(function() {
var lastTime = 0;
var vendors = ['ms', 'moz', 'webkit', 'o'];
for(var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
window.requestAnimationFrame = window[vendors[x]+'RequestAnimationFrame'];
window.cancelAnimationFrame = window[vendors[x]*'CancelAnimationFrame'] || window[vendors[x]*'CancelRequestAnimationFrame'];
}
if (!window.requestAnimationFrame)
window.requestAnimationFrame = function(callback, element) {
var currTime = new Date().getTime();
var timeToCall = Math.max(0, 16 - (currTime - lastTime));
var id = window.setTimeout(function() { callback(currTime + timeToCall); },
timeToCall);
lastTime = currTime + timeToCall;
return id;
};
if (!window.cancelAnimationFrame)
window.cancelAnimationFrame = function(id) {
clearTimeout(id);
};
}());
function randRange(min, max) {
return Math.random() * ( max - min ) + min;
}
var canvas = document.getElementById('fourth-grad');
var cntxt2d = canvas.getContext('2d');
canvas.width = 579;
canvas.height = 300;
var smallCanvas = document.getElementById('small-two');
var bufcntxt = smallCanvas.getContext('2d');
smallCanvas.width = 20;
smallCanvas.height = 20;
var rGrad = cntxt2d.createRadialGradient(10,10,0,10,10,10);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(252, 194, 18, 0)');
bufcntxt.fillStyle = rGrad;
bufcntxt.beginPath();
bufcntxt.arc(10,10,10, 0, Math.PI*2, false);
bufcntxt.fill();
var dotImage = bufcntxt.getImageData(0,0,20,20);
function renderR(){
cntxt2d.clearRect(0,0,759,300);
var len = 150;
while(len--){
cntxt2d.drawImage(smallCanvas, randRange(10,549) , randRange(10, 270));
}
window.requestAnimationFrame(renderR);//this makes this function recursive
}
renderR();
</script>

If this is annoying you, sorry. The most important part in the `render()` function is the `clearRect()` method. It clears the canvas in the area defined by the four parameters it takes.

We also defined a function we'll use next, `randRange(min, max)`, it returns a random float number between the two numbers provided as `min` and `max`.

To animate with some order we'll need something more than just the renderer function, we'll build a `animationLoop()` function that will call the following methods on each frame.

-   `genParticles()` This will generate the particles as object and store them in the `particleList` array.
-   `positionParticles()` This will calculate the position of each particle and kill old particles.
-   `render()` This will render the `dotImage` on the canvas according to the position and age of each particle.
-   `requestAnimationFrame(animationLoop)` This will call this function on the next frame.

We must initialize the global variables and define the functions, so the cod so far should look something like this (assuming you already have the polyfill and the randRange function).

```js
var ratePF = 1;
var particleList = [];
var originPoint = { x:405, y:391 };
var canvas = document.getElementById('vapour');
var cntxt2d = canvas.getContext('2d');
var running = true;
var particleLife = 100;
canvas.width = 579;
canvas.height = 695;
var bufferCanvas = document.getElementById('dot-buffer');
var bufcntxt = bufferCanvas.getContext('2d');
bufferCanvas.width = 10;
bufferCanvas.height = 10;
var rGrad = bufcntxt.createRadialGradient(5,5,0,5,5,5);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(52, 14, 208, 0)');
bufcntxt.fillStyle = rGrad;
bufcntxt.fillRect(0,0,10,10);
var dotImage = bufcntxt.getImageData(0,0,10,10);
bufcntxt.putImageData(dotImage,0,0);
function genParticles(){
var len = ratePF;
while(len--){
var oneParticle = {
x: originPoint.x,
y: originPoint.y,
velx: randRange(-1.2,0.8),//this is the range of speed on the x-axis
vely: randRange(-3,-0.75),//this is the range of speed on the y-axis
life: particleLife,//this will be reduced by one on each frame.
totalLife: particleLife,
maxSize: randRange(60,100)
};
oneParticle.x -= oneParticle.velx;
oneParticle.y -= oneParticle.vely;
particleList.push(oneParticle);
}
}
function positionParticles(){
var len = particleList.length;
while(len--){
var op = particleList[len];//op = one particle
op.life--;
if(op.life < 1){
particleList.splice(len,len+1);
continue;
}
//if(!len%1000)console.log(op.life);
op.x += op.velx;
op.y += op.vely;
}
}
function render() {
cntxt2d.clearRect(0,0,window.innerWidth,window.innerHeight);
var len = particleList.length;
while(len--){
var op = particleList[len];//op = one particle
ga = op.life / op.totalLife;
cntxt2d.globalAlpha = ga;
var size = op.maxSize - op.maxSize * ga + 10;
cntxt2d.drawImage(bufferCanvas, op.x, op.y, size, size);
}
}
function animationLoop(){
genParticles();
positionParticles();
render();
if(running) {
requestAnimationFrame(animationLoop);
}
}
animationLoop();
```


<canvas id="vapour" style="background:url('img/tetera-canvas.jpg');max-width:100%"></canvas>
<canvas id="dot-buffer" style="display:none"></canvas>
<script>
var ratePF = 1;
var particleList = [];
var originPoint = { x:405, y:391 };
var canvas2 = document.getElementById('vapour');
var cntxt2d2 = canvas2.getContext('2d');
var running = true;
var particleLife = 100;
canvas2.width = 579;
canvas2.height = 695;
var bufferCanvas = document.getElementById('dot-buffer');
var bufcntxt = bufferCanvas.getContext('2d');
bufferCanvas.width = 10;
bufferCanvas.height = 10;
var rGrad = bufcntxt.createRadialGradient(5,5,0,5,5,5);
rGrad.addColorStop(0,'rgba(252, 194, 18, 1)');
rGrad.addColorStop(1,'rgba(52, 14, 208, 0)');
bufcntxt.fillStyle = rGrad;
bufcntxt.fillRect(0,0,10,10);
var dotImage = bufcntxt.getImageData(0,0,10,10);
bufcntxt.putImageData(dotImage,0,0);
function genParticles(){
var len = ratePF;
while(len--){
var oneParticle = {
x: originPoint.x,
y: originPoint.y,
velx: randRange(-1.2,0.8),//this is the range of speed on the x-axis
vely: randRange(-3,-0.75),//this is the range of speed on the y-axis
life: particleLife,
totalLife: particleLife,
maxSize: randRange(60,100)
};
oneParticle.x -= oneParticle.velx;
oneParticle.y -= oneParticle.vely;
particleList.push(oneParticle);
}
}
function positionParticles(){
var len = particleList.length;
while(len--){
var op = particleList[len];//op = one particle
op.life--;
if(op.life < 1){
particleList.splice(len,len+1);
continue;
}
//if(!len%1000)console.log(op.life);
op.x += op.velx;
op.y += op.vely;
}
}
function render() {
cntxt2d2.clearRect(0,0,window.innerWidth,window.innerHeight);
var len = particleList.length;
while(len--){
var op = particleList[len];//op = one particle
ga = op.life / op.totalLife;//proportion of life the particle has left
cntxt2d2.globalAlpha = ga;
var size = op.maxSize - op.maxSize * ga + 10;
cntxt2d2.drawImage(bufferCanvas, op.x, op.y, size, size);
}
}
function animationLoop(){
genParticles();
positionParticles();
render();
if(running) {
requestAnimationFrame(animationLoop);
}
}
animationLoop();
</script>

This rather dramatic concoction looks better for this image than a white vaporous cloud.

I did a codepen [Gradient particles on canvas](http://cdpn.io/ACDbI) with this code, where you can play with the variables.

This is just the basics with `<canvas>` and I must say: I'm impressed with the performance; in the end the math behind particles are always the same, the drawing API wasn't complicated and easier than others I've seen.
