title: En la práctica: ¿Se puede usar SVG confiablemente como imagen?\
subtitle: Nuestra respuesta, durante mucho tiempo, había sido: NO, porque IE y Android no lo ven; sólo se podía en los estilos como una mejora de CSS. Pero estábamos equivocados. Sólo necesitamos un poco de Modenizr.\
classes: language-markup\
pubDate:2012-10-29 10:46:25

<header>
<hgroup>
En la práctica: ¿Se puede usar SVG confiablemente como imagen?
==============================================================

Claro que si! Usando una combinación de *noscript*, Modernizr y CSS.
--------------------------------------------------------------------

</hgroup>
</header>
Preámbulo {#ac1}
---------

[Saltarse esta lata](#ac2)

Cualquiera que halla llegado aquí sabrá ya de los beneficios de [SVG (Scalable Vector Graphics)](http://es.wikipedia.org/wiki/Scalable_Vector_Graphics), así que no explicaré qué es. Hace tiempo hay polyfills de SVG para IE o Android y algunos otros pero siguen siendo parches que requieren el uso de Flash u otras bibliotecas de javascript, como si ya hubiese poco javascript del que preocuparse en nuestras páginas. Así que nosotros habíamos evitado su uso, fuera de uno que otro gráfico de fondo en CSS como *mejoras progresivas*; mas, el uso que siempre quise darle era para el logo de un sitio web, que según [nuestras políticas de diseño](?t=article&a=Propuesta-de-Normas-de-desarrollo.txt) el logo va en el DOM. Desde la salida de las pantallas *retina* y otras de alta resolución, SVG tiene más sentido que nunca.

Como muchos logos ni siquiera tienen gradientes, son muy buenos candidatos para ser puestos como SVG en los sitios web, pero como no conocíamos una manera de incrustar SVG con confianza, pues no lo hacíamos. Según [HTML5 please](http://html5please.com/) y [Can I use](http://caniuse.com/#feat=svg-img) SVG tiene bastante buen soporte, pero IE 8, FF 3.6 y el navegador de Android 2.3 no tienen soporte. Resulta que en Chile, Internet Explorer 8 o menor, aún es una gran parte de los navegadores usados, particularmente en oficinas.

El otro día vi un post de Chris Coyier en smashing magazine que usaba [Modernizr](http://modernizr.com/) para detectar soporte, pero desestima el soporte de IE8 como si nadie lo usara y deja el tema ahí. Pero como nosotros hemos estamos trabajando con [picturefill(Haz click para ver el sitio del proyecto en Github)](https://github.com/scottjehl/picturefill) que usa `<noscript>` exitosamente se me ocurrió cómo abordarlo simple e infaliblemente.

*<span lang="fr">Mise en place</span>*, utensilios de trabajo {#ac2}
-------------------------------------------------------------

Como ejemplo cambiaremos que el logo de este blog a SVG con *<span lang="en">fallback</span>* de PNG.

Necesitaremos:

-   El [logo en SVG (ver el logo en vectores)](img/content/logo-code-medula-diseno.svg)
-   EL [logo en PNG (ver el logo en PNG)](img/content/logo-blog-medula-code.png) o GIF
-   [Modernizr](http://modernizr.com/)

Yo ya tengo Modernizr cargando en la página probando si hay soporte para una serie de cosas y porque viene con el HTML5 shiv. Hoy en día es un *<span lang="en">standard</span>* de la industria, si no está ya en una página, se puede generar una versión que pruebe solamente su hay SVG y genere la clase en el html.

Lo que hoy hay en el sitio es los siguiente:

<div class="mainContainer">
<header class="contiene-logo">
<a href="./"><img src="img/content/logo-blog-medula-code.png" alt="Logotipo Médula Diseño Code Blog"></a>

</header>
<!-- otras cosas -->
</div>
Usaremos el mismo .png actual como alternativa al SVG así que con esto estamos listos para empezar.

Armado {#ac3}
------

### *<span lang="en">Markup</span>* {#ac4}

Sólo debemos meter la imagen actual dentro de un `<noscript>`.

<div class="mainContainer">
<header class="contiene-logo">
<a href="./"><noscript>\
<img src="img/content/logo-blog-medula-code.png" alt="Logotipo Médula Diseño Code Blog">

</noscript>
</a>

</header>
<!-- otras cosas -->
</div>
### CSS {#ac5}

Probemos qué pasa si lo probamos sin hacer cambios al CSS... la imagen desaparece completamente, ni siquiera hay una llamada al servidor. Bien hasta ahora.

Ahora definamos qué pasa si hay o no soporte para SVG. Primero definir el contenedor de la imagen y luego la fuente según haya soporte o no.

bc(language-css).\
.contiene-logo a{\
display: block;\
min-width: 281px;\
min-height: 120px;\
}

Podríamos agregar el PNG de fondo aquí, pero haríamos que cargue el PNG inmediatamente y si luego hubiese soporte SVG cargaríamos el SVG también, lo que no es amigable para las conexiones lentas.

Cuando Modernizr entra en acción y encuentra soporte o no para SVG lo siguiente va a ser interpretado.

bc(language-css).\
.svg .contiene-logo a{\
background-image: url(../img/content/logo-code-medula-diseno.svg);\
}\
.no-svg .contiene-logo a{\
background-image: url(../img/content/logo-blog-medula-code.png);\
}

Entonces si probamos esto ahora cargará el SVG sin problemas y si lo abro en IE8 carga el PNG. Resultó tan fácil que ahora haré que el logo sea un [sprite SVG (ver el sprite SVG aquí)](img/content/logo-code-medula-diseno-sprite.svg) y como es un poco más de texto que se repite no habrá mucha diferencia de peso (usando gzip).

bc(language-css).\
.svg .contiene-logo a{\
background-image: url(../img/content/logo-code-medula-diseno-sprite.svg);\
}\
.svg .contiene-logo a:link, .svg .contiene-logo a:visited{\
background-position: 0 0;\
}\
.svg .contiene-logo a:hover, .svg .contiene-logo a:active, .svg .contiene-logo a:focus{\
background-position: 0 -120px;\
}

Por supuesto que esto lo hice en SASS y se ve de la siguiente manera.

bc(language-scss\#ac7).\
.contiene-logo{\
a{\
display: block;\
min-width: 281px;\
min-height: 120px;\
.no-svg &{\
background-image: url(../img/content/logo-blog-medula-code.png);\
}\
.svg &{\
background-image: url(../img/content/logo-code-medula-diseno-sprite.svg);\
&:link, &:visited{\
background-position: 0 0;\
}\
&:hover, &:active, &:focus{\
background-position: 0 -120px;\
}\
}\
}\
}

Otros usos o implementaciones. {#ac6}
------------------------------

Esto no necesita más javascript más allá de cargar Modernizr, pero con un poco trabajo se podría adaptar para hacer algo similar a lo que hace Picturefill, identificando las capacidades del navegador y luego cargando lo que corresponda y en su defecto, una imagen dentro de `<noscript>`. De hecho se podría hacer una especie de *<span lang="en">plugin</span>* para Picturefill.

Si te interesa que escriba sobre cómo hacerlo en JS sin CSS, pues me lo comentas y veré de agregarlo a este artículo.

***EDIT:** He descubierto 2 detalles con esta técnica, un problema y una observación.*

### Observación en Firefox

En Firefox los fondos en SVG al parecer son convertidos a bitmap y no son re-calculados cuando uno hace zoom **<span class="button">Ctrl</span>** + **<span class="button">+</span>** en la página, por lo que para ese uso no tiene mucho sentido usar SVG, traté de corregirlo cambiando las unidades de medida de px a em, pero nada. Esto no ocurre si usamos una imagen directamente.

### Problema en Opera

En Opera 12, al hacer zoom, el fondo se recorta, como si el SVG se escalara a diferente proporción que el contenedor, traté cambiando las unidades de medida de px a em y nada. Luego intenté con diferentes formas de configurar `background-size` pero tampoco sirvió de mucho, se vio un cambio, pero sigue siendo malo el recorte.

*fin del EDIT*

He estado trabajando en un artículo sobre cómo usar **Picturefill** para tener imágenes responsivas en un sitio; pero en la mitad se me ocurrió esto así que lo publico primero.

Por favor, si te sirve esto, coméntame cómo lo quieres usar o lo has usado o si he escrito puras cabezas de pescado.
