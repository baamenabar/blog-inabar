title: Imágenes responsivas con Picturefill\
subtitle: Diagramar un sitio de modo “responsive“ es sólo una parte, no menos importante es el manejo de imágenes, con picturefill tenemos una herramienta para controlar las imágenes en diferentes formatos y resoluciones.\
classes: language-markup\
pubDate:2013-01-18 21:43:19

<header>
<hgroup>
Imágenes *<span lang="en">responsive</span>* con Picturefill
============================================================

Cómo entregar diferentes imágenes de acuerdo al tamaño de pantalla del dispositivo.
-----------------------------------------------------------------------------------

</hgroup>
</header>
*Published* / Publicado : <time datetime="2012-10-11 02:09:46">2012-10-11 02:09:46</time> | *By*: Agustín Amenabar L.

Preámbulo {#ac1}
---------

[Saltarse esta lata](#ac2)

### Teoría de lo que vamos a usar

Asumiendo que todo el concepto *Responsive Web Design*, o diseño web responsivo está entendido, un aspecto práctico bien importante es el manejo de imágenes.

-- Eso es cuento viejo! en CSS se pone: `img{ max-width:100%; }` y listo!

Si, pero no todas las pantallas son iguales, no tienen el mismo tamaño, resolución, ni tienen la misma orientación; en muy poco casos es necesario cargar la **imagen de súper resolución HD para pantallas *retina* de 2800 pixeles de ancho**. ¿Por qué entonces entregársela a todos los dispositivos sin discriminación de tamaño? A veces sólo necesitamos la imágen a 240px de ancho.

-- OK, pero igual eso se hace en CSS, cambias el fondo según el ancho u otro *<span lang="en">media query</span>* y listo!

Si, pero no todas las imágenes son presenctacionales, o sea, muchas pertenecen al contenido y deben estar en el HTML y no en la hoja de estilos. Para eso usaremos **Picturefill** y generaremos imágenes *<span lang="en">responsive</span>*.

En este caso elegimos Picturefill, dado que es de las pocas soluciones relativamente semánticas que contempla el caso de uso de **dirección de arte** en las imágenes responsivas. Además que [Scott Jehl](https://twitter.com/scottjehl) y [Mat “Wilto” Marquis](https://twitter.com/wilto) han sido muy [persuasivos y polémicos](http://timkadlec.com/2012/05/wtfwg/) promoviendo este [*polyfill*(definición de polyfill en la wikipedia)](http://en.wikipedia.org/wiki/Polyfill) por lo que estaba buscando la excusa de probarlo.

Desde que salió el *polyfill* original se ha incluido `<picture>` como una “propuesta de extensión” de la especificación HTML con algunas modificaciones a la propuesta inicial que incluyen el atributo `srcset`; *Wilto* hizo un buen ejemplo con una [corta pero buena explicación(Visitar la página del ejemplo explicado)](http://wil.to/picturefill/) del uso de los atributos `srcset` y `media` propuestos y como funcionaría de estar implementado.

Inicialmente estaba muy emocionado de poder usar el nuevo elemento `<picture>` pero es [**tan** complicado](https://github.com/scottjehl/picturefill/issues/29#issuecomment-6880729) que no vale la pena en esta etapa, así que nos quedaremos con los DIVs.

También existe la opción de usar el *<span lang="en">branch</span>* `div-markup-currentprop` del repositorio de picturefill y así jugar con srcset incluso en imágenes, pero es agregar una variable más al tema, prefiero partir por lo simple. Llegar a esta conclusión me tomó un día entero metido leyendo repositorios, comparaciones de códigos, escasa documentación y reportes de bugs.

*<span lang="fr">Mise en place</span>*, utensilios de trabajo {#ac2}
-------------------------------------------------------------

Vamos a replicar un poco el `index.html` del repositorio de **picturefill** para hacer una pueba de concepto, pero con 2 juegos de imágenes, para eso necesitaremos:

-   Un HTML para contener todo; lo único que **debe** tener es el `<meta name="viewport" content="width=device-width,initial-scale=1">`.
-   La biblioteca [Picturefill(Página del proyecto en Github)](https://github.com/scottjehl/picturefill)
-   Un [build base de Modernizr](http://modernizr.com/download/#-fontface-backgroundsize-borderimage-borderradius-boxshadow-flexbox-flexboxlegacy-hsla-multiplebgs-opacity-rgba-textshadow-cssanimations-csscolumns-generatedcontent-cssgradients-cssreflections-csstransforms-csstransforms3d-csstransitions-canvas-localstorage-shiv-teststyles-testprop-testallprops-prefixes-domprefixes-load) para tomar los shivs y mini ayudas que trae, que uno usa siempre, ojo que tiene que ir sin detección de *<span lang="en">media queries</span>*, porque vamos a cargar una especial para IE a continuación.
-   La [rama especial de matchMedia.js(Página del repositorio en github)](https://github.com/benschwarz/matchMedia.js/tree/IE7-8) para IE7 y 8, [sin la cual picturefill será ignorado en IE7 y 8(Página del issue nº 33)](https://github.com/scottjehl/picturefill/issues/33)
-   El *<span lang="en">polyfill</span>* CSS3 Media queries JS <http://code.google.com/p/css3-mediaqueries-js/> para reemplazar Respond JS, porque entra en conflicto con la versión para IE8 o menor de MatchMedia.js
-   Dos series de imágenes en diferentes tamaños y recortes.

Todo esto está en el [repositorio del demo](https://github.com/baamenabar/article-picturefill) .

### Las imágenes {#ac2a}

Haremos el ejercicio con 2 imágenes en sus diferentes versiones.

#### Callampas {#ac2a1}

<div class="pictureList">
\* ![](img/content/ricpf/thumbs/callampas-430.jpg):img/content/ricpf/callampas-430.jpg

\* ![](img/content/ricpf/thumbs/callampas-570.jpg):img/content/ricpf/callampas-570.jpg

\* ![](img/content/ricpf/thumbs/callampas-770.jpg):img/content/ricpf/callampas-770.jpg

\* ![](img/content/ricpf/thumbs/callampas-1024.jpg):img/content/ricpf/callampas-1024.jpg

\* ![](img/content/ricpf/thumbs/callampas-2000.jpg):img/content/ricpf/callampas-2000.jpg

</div>
#### Flores amarillas {#ac2a2}

Este segundo set incluye versión 2x (para *retina*) en la mayoría de las versiones e intencionalmente tiene las imágenes recortadas de diferentes maneras.

<div class="pictureList">
<!-- Un manchón de flores Calceolaria filicaulis, en la Cordillera Nevada, Lago Ranco, Chile -->
\* ![](img/content/ricpf/thumbs/flores-full-full.jpg):img/content/ricpf/flores-full-full.jpg

\* ![](img/content/ricpf/thumbs/flores-huge.jpg):img/content/ricpf/flores-huge.jpg

\* ![](img/content/ricpf/thumbs/flores-big.jpg):img/content/ricpf/flores-big.jpg

\* ![](img/content/ricpf/thumbs/flores-big-2x.jpg):img/content/ricpf/flores-big-2x.jpg

\* ![](img/content/ricpf/thumbs/flores-medium.jpg):img/content/ricpf/flores-medium.jpg

\* ![](img/content/ricpf/thumbs/flores-medium-2x.jpg):img/content/ricpf/flores-medium-2x.jpg

\* ![](img/content/ricpf/thumbs/flores-small-landscape.jpg):img/content/ricpf/flores-small-landscape.jpg

\* ![](img/content/ricpf/thumbs/flores-small-2x-landscape.jpg):img/content/ricpf/flores-small-2x-landscape.jpg

\* ![](img/content/ricpf/thumbs/flores-small.jpg):img/content/ricpf/flores-small.jpg

\* ![](img/content/ricpf/thumbs/flores-small-2x.jpg):img/content/ricpf/flores-small-2x.jpg

</div>
Instalando {#ac4}
----------

Al documento HTML le agregaremos los siguientes scripts en el `head`:

bc..

<head>
<!-- [...] -->
<script src="matchmedia.js">
</script>
<script src="picturefill.js">
</script>
<!-- [...] -->
</head>
Picturefill realmente necesita sólo `picturefill.js`. Presonalmente prefiero ponerlo dentro del `<head>` del documento junto al resto de los scripts que hacen funcionar mínimamente la página, aunque igual espera a que el documento esté cargado.

Si queremos que funcione en navegadores sin *<span lang="en">media queries</span>*, como Internet Explorer 8 o menor, debemos agregar esta versión para IE 7y8 de `matchmedia.js`. Si no queremos usar un matchmedia especial o por alguna razón entra en conflicto con otro script está la opción *oficial* para que funcione en IE 7 y 8 que veremos más abajo.

Sólo nos queda agregar **Modernizr** y soporte *<span lang="en">responsive</span>* para IE8 o menor.

bc..

<head>
<!-- [...] -->
<script src="modernizr.custom.89870.js">
</script>
<script src="matchmedia.js">
</script>
<script src="picturefill.js">
</script>
<!-- [if lt IE 9]>
    <script src="css3-mediaqueries.js"></script>
<![endif]-->
<!-- [...] -->
</head>
Modernizr siempre al principio, para que tengamos inmediatamente disponibles sus métodos y clases en caso de necesitarlas.

**Advertencia!** Para quienes quieren que su diagramación *<span lang="en">responsive</span>* funcione en IE, como cuando se hace un diseño móvil primero. Deben saber que [Respond.js](https://github.com/scottjehl/Respond) (el script más usado para eso) entra en conflicto con la versión de [Matchmedia.js](https://github.com/benschwarz/matchMedia.js/tree/IE7-8) que estamos usando; por lo que debemos usar el *<span lang="en">polyfill</span>* `css3-mediaqueries.js` que es bastante más pesado, pero con los condicionales de IE lo haremos cargar sólo cuando sea necesario.

Sólo nos queda advertir que en el `<head>`, para ver correctamente lo que estamos haciendo en un dispositivo móvil, **debe** ir lo siguiente: `<meta name="viewport" content="width=device-width,initial-scale=1">` o algo similar (si sabes de qué se trata).

Markup tipo &lt;picture&gt; {#ac5}
---------------------------

### Simple primero {#ac6}

Picturefill es una colección de elementos `<div>` con `data-attributes` que son leídos por Javascript e inserta la imagen que corresponda según se cumplan diferentes *<span lang="en">media queries</span>*. En el cuerpo del documento pegamos lo siguiente.

bc..

<div data-picture data-alt="Callampas, probablemente Deconica coprophila, encontradas en la Cordillera Nevada, Lago Ranco, Chile.">
<!-- la primera opción, móvil primero, sin match-media -->
<div data-src="imgs/callampas-430.jpg">
</div>
<!-- Opción dos, cualquier cosa más grande de 480px de ancho se gana una foto tamaño mediano -->
<div data-src="imgs/callampas-570.jpg"  data-media="(min-width: 480px)">
</div>
<!-- Opción tres, cualquier cosa más grande de 760px de ancho (como un iPad en vertical): se gana una foto tamaño tableta -->
<div data-src="imgs/callampas-770.jpg"  data-media="(min-width: 760px)">
</div>
<!-- Opción cuatro, cualquier cosa más grande de 960px de ancho (como un iPad en horizontal): se gana una foto tamaño escritorio -->
<div data-src="imgs/callampas-1024.jpg" data-media="(min-width: 960px)">
</div>
<!-- Opción cinco, cualquier cosa más grande de 1280px de ancho se gana una foto tamaño gigante -->
<div data-src="imgs/callampas-2000.jpg" data-media="(min-width: 1280px)">
</div>
<!-- Contenido alternativo para navegadores sin JS. La misma fuente de la imagen inicial. -->
<noscript>
<img src="imgs/callampas-430.jpg" alt="Callampas, probablemente Deconica coprophila, encontradas en la Cordillera Nevada, Lago Ranco, Chile.">

</noscript>
</div>
Se puede ver este [demo funcionando aquí](http://baamenabar.github.com/article-picturefill/v1.html) . En el código de arriba tenemos un `<div>` contenedor con el atributo `data-picture` que están reemplazando al elemento `<picture>`. Dentro de éste hay otros *<span lang="html">div</span>* con los atributos `data-src` y algunos con `data-media` que hacen las veces del elemento `<source>` con sus atributos:

`data-src` define la imagen a cargar si se cumplen las condiciones definidas en el atributo `data-media` igual como un @ @media @ de CSS.

### ¿Y las pantallas retina o HD? {#ac7}

Lo anterior está muy bien, pero cuando se tienen estas super pantallas de alta definición (*retina* o similar), hay sacarles el jugo, pero la idea es hacerlo sin castigar a los dispositivos de más baja resolución. Para eso en el atributo `data-media` haremos *<span lang="en">media queries</span>* para que las pantallas de alta resolución carguen imágenes con más pixeles. Hasta hace poco hacer una sola condición para pantallas retina terminaba en algo como lo siguiente:[^1]

bc(language-css)..\
@media\
screen and (-webkit-min-device-pixel-ratio: 2),\
screen and ( min--moz-device-pixel-ratio: 2),\
screen and ( -o-min-device-pixel-ratio: 2/1),\
screen and ( min-device-pixel-ratio: 2),\
screen and ( min-resolution: 192dpi) {

/\* cosas para retina \*/

}

Lo que ni siquiera consideraba ancho u otras combinaciones. Afortunadamente el CSS WC re-definió el tema de la resolución y los navegadores lo han adoptado en su gran mayoría. El *<span lang="en">media query</span>* es `min-resolution` y se mide en **dpi** . Usar dpi (puntos por pulgada) me parece un poco anacrónico y corto de vista; afortunadamente no he sido el único y una nueva forma de medir la resolución en CSS viene en camino: `min-resolution` medido en dppx (*<span lang="en">dots per pixel</span>* o puntos por pixel) que se puede usar hoy, pero tiene algunas implementaciones raras, así que lo evitaremos por el momento.[^2]

Entonces, según lo anterior, hacemos el siguiente armado de picturefill para probar nuestros dispositivos, sus navegadores y la sintaxis que estamos usando:

bc..

<div data-picture data-alt="Un manchón de flores Calceolaria filicaulis, en la Cordillera Nevada, Lago Ranco, Chile.">
<!-- la primera opción, móvil primero, sin match-media -->
<div data-src="imgs/flores-small.jpg">
</div>
<!-- Opción dos, sólo si detectamos resolución alta cargar la versión 2x -->
<div data-src="imgs/flores-small-2x.jpg"    data-media="(-webkit-min-device-pixel-ratio: 1.5), (min-resolution: 144dpi)">
</div>
<noscript>
<img src="imgs/flores-small.jpg" alt="Un manchón de flores Calceolaria filicaulis, en la Cordillera Nevada, Lago Ranco, Chile.">

</noscript>
</div>
El [demo funcionando está aquí](http://baamenabar.github.com/article-picturefill/v2.html) y es bastante simple; sólo detecta la resolución del dispositivo y carga la imagen **2x** si cumple con el mínimo de resolución establecido en el *<span lang="en">media query</span>*. Si usamos `-webkit-min-device-pixel-ratio: 2` sólo estaremos apuntando a las pantallas retina, personalmente aún no me entero de un Android o Windows que tenga tan alta resolución, por eso prefiero usar **1.5**.

Poco chiste tiene esto, por eso ahora vamos a darle todo el rango de anchos para alta y baja resolución, [el resultado lo podemos ver aquí](http://baamenabar.github.com/article-picturefill/v3.html) .

bc..

<div data-picture data-alt="Un manchón de flores Calceolaria filicaulis, en la Cordillera Nevada, Lago Ranco, Chile.">
<!-- la primera opción, móvil primero, sin match-media -->
<div data-src="imgs/flores-small.jpg">
</div>
<div data-src="imgs/flores-small-2x.jpg"    data-media="(-webkit-min-device-pixel-ratio: 1.5), (min-resolution: 144dpi)">
</div>
<div data-src="imgs/flores-medium.jpg"      data-media="(min-width: 480px)">
</div>
<div data-src="imgs/flores-medium-2x.jpg"   data-media="(min-width: 480px) and (-webkit-min-device-pixel-ratio: 1.5), 
                                                        (min-width: 480px) and (min-resolution: 144dpi)">
</div>
<div data-src="imgs/flores-big.jpg"         data-media="(min-width: 780px)">
</div>
<div data-src="imgs/flores-big-2x.jpg"      data-media="(min-width: 780px) and (-webkit-min-device-pixel-ratio: 1.5), 
                                                        (min-width: 780px) and (min-resolution: 144dpi)">
</div>
<div data-src="imgs/flores-huge.jpg"        data-media="(min-width: 1280px)">
</div>
<div data-src="imgs/flores-full-full.jpg"   data-media="(min-width: 1280px) and (-webkit-min-device-pixel-ratio: 1.5), 
                                                        (min-width: 1280px) and (min-resolution: 144dpi)">
</div>
<!--[if (lt IE 9) & (!IEMobile)]>
<div data-src="imgs/flores-big.jpg"></div>
<![endif]-->
<noscript>
<img src="imgs/flores-small.jpg" alt="Un manchón de flores Calceolaria filicaulis, en la Cordillera Nevada, Lago Ranco, Chile.">

</noscript>
</div>
Hemos agregado aquí la otra alternativa para entregar contenido a IE8 o menor, mediante los condicionales de IE. Asumiendo que IE7-8 está funcionando en un escritorio y que no estamos haciendo una diagramación **móvil primero**, le entregamos una imagen grande, sin esto y sin el match-media especial, lo que vería un Internet Explorer sería la primera de la lista, la que no tiene `data-media`.

Y esto es sólo el principio, se puede definir según orientación, si el dispositivo es blanco y negro, si es impresión o pantalla, etc.

Epílogo {#ac8}
-------

Llevo casi 3 meses escribiendo este artículo, desde que empecé hasta ahora han pasado muchas cosas, como que me he unido al [Responsive Image Comunity Group](http://responsiveimages.org/) y leo ávidamente el mailing list. He hecho unos pequeños aportes para el proyecto y estoy trabajando en más. La propuesta del elemento `<picture>` tiene bastante que madurar aún, y no es una propuesta menor, significa cambiar un montón de cosas en los navegadores, por eso ha habido tanta resistencia.

En el camino me he metido con lo que es formatos de imágenes más modernos (SVG o webP) y cómo usarlos hoy en día, más sobre eso, pronto.

Algunas fuentes {#ac9}
---------------

[Responsive Images](http://responsiveimages.org/)

[^1]: [CSS WG Blog -- How To Unprefix -webkit-device-pixel-ratio](http://www.w3.org/blog/CSS/2012/06/14/unprefix-webkit-device-pixel-ratio/)

[^2]: [CSS WG Blog -- How To Unprefix -webkit-device-pixel-ratio](http://www.w3.org/blog/CSS/2012/06/14/unprefix-webkit-device-pixel-ratio/)
