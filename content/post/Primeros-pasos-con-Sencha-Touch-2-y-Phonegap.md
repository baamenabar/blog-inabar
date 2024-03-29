title: Primeros tropiezos con Sencha Touch 2\
subtitle: Un registro de errores, soluciones y links al empezar a trabajar con Sencha Touch 2, para el desarrollo de aplicaciones móviles.\
pubDate:2012-10-02 17:13:37

Primeros tropiezos con Sencha Touch 2
=====================================

Estoy armando un app (por primera vez), ya me he decantado por phoneGap y hoy tuve qe hacer la selección entre Sencha, XUI y jQuery Mobile. Cuando me di cuenta que jQuey Mobile es un plugin sobre jQuery lo descarté, en <i>mobile</i> no necesito ni la mitad de todo lo que trae jQuery.

Cuando vi el <a href="http://dev.sencha.com/deploy/touch/examples/production/kitchensink/">demo</a> de "Kitchen Sink" de Sencha Touch 2, lo elegí.

Hasta ahí íbamos bien.

Descargué sencha, que <em>hay</em> que descomprimir en una carpeta del servidor local (yo uso WAMP)

<blockquote>
<code>C:\\htdocs\\sencha</code>

</blockquote>
Navegas hasta la carpeta <code>http://localhost/sencha/</code> y aparece una página de bienvenida con un video de "Primeros pasos" en inglés y en un MAC.

Yo tengo un PC con windows 7 de 64 bits, con un suspiro apreté play.

Cinco segundos y ya estaba abierta la línea de comando, en el video navegan hasta la carpeta donde está instalado sencha y parten inicalizando una carpeta con el siguiente comando

<blockquote>
<kbd>sencha generate app GS ../GS</kbd>

</blockquote>
hago lo mismo y

<blockquote>
<code>no se esperaba \* SenchaSDKTools \* en este momento </code>

</blockquote>
<aside>
Luego de una breve confusión descubro que, en esta etapa no necesito bajar el <q>Sencha SDK Tools 2.0 Beta for Developers</q>.

</aside>
Pero sigo con el problema, así que buscando doy con el siguiente artículo de Sasha dos Santos

<http://sashageekette.wordpress.com/2012/03/24/getting-started-with-sencha-touch-2-0-on-windows-7-x64/>

Donde me aclara que debo navegar hasta la carpeta donde está sencha descomprimido y entrar en la carpeta <code>command</code> ahí lo intento nuevamente:

<blockquote>
<code>"jsdb" no se reconoce como un comando interno o externo, programa o archivo por lotes ejecutables.</code>

</blockquote>
Lo que para mí es chino, sigo leyendo el artículo y creo hacer lo que dice, pero luego de media hora me doy cuenta que hay una línea que no estaba leyendo bien, así que las instrucciones, en español y para los perdidos (como yo) son las siguientes:

<ol>
<li>
En el CMD navegar hasta la carpeta donde sencha SDK está descomprimido <q><kbd>C:\\htdocs\\sencha</kbd></q>

</li>
<li>
Escribir: <kbd>command\\sencha</kbd> si aparecen una serie de líneas explicando el uso, está ok; si no asegurarse de que estar ejecutando los comandos desde la raíz de la instalación de sencha.

</li>
<li>
finalmente <kbd>command\\sencha generate app GS ../GS</kbd> ojo con las barras, la primera es Windows, la segunda LINUX

</li>
</ol>
Carpeta generada! sigamos con el video.

El video presenta a una velocidad impresionante cómo ir armando tu primer app.

Todo bien hasta que me salgo del guión, en cierto momento cargan datos JSON desde un feed de feeburner. Y si sigo los pasos, ni un problema. Pero si genero mis propios

En el fondo estoy convirtiendo un array con los datos cargados de la página.

<q><samp>"Unexpected token :"</samp></q>\
necesitaba JSON-P

JSON-P
------

Ni idea qué es ni como hacer JSON-P en PHP, pero doy con este artículo de Torleif Berger <http://www.geekality.net/2010/06/27/php-how-to-easily-provide-json-and-jsonp/> donde explica cómo generar JSON-P con PHP.

Sólo que su método de validación es un poco muy estricto y me firltra la manera en que Sencha Touch 2 nombra los pedidos callback. Le hago un pequeño hack al código de Torlief y quedo funcionando OK.

<figure>
<blockquote>
<code>if(is\_valid\_callback(implode('',explode('.',\$\_GET\['callback'\]))))</code>

</blockquote>
</figure>
Lo que también es útil es agregar sencha al path, porque así, una vez trabajando en un app, desde el cmd uno puede generar controllers muy fácil:

<figure>
<blockquote>
<kbd>sencha generate controller NombreDeUnControlador</kbd>

</blockquote>
</figure>
Sigo con el tutorial, es fácil seguir instrucciones pero entender como para hacer lo que quiero está bien complicado, aún así llego hasta la parte donde se publica como web-app, con el siguiente comando:

<figure>
<blocquote>\
<kbd>command\\sencha app build production</kbd>\
</blocquote>

<figcaption>
Esto creará una carpeta una carpeta build/production/ dentro de la carpeta del proyecto. Dentro de esa carpeta estará el proyecto “minificado&rdquo;

</figcaption>
</figure>
Luego de un rato tenemos una ágil aplicación corriendo en la carpeta build.

Es esta misma compilación la que se debe usar para empaquetar el app para Android o iOS, en un principio yo empaquetaba el app completo, y claro, pesaba 40 megas pero desde la carpeta compilada es mucho más rápido también(me costó un par de semanas descubrir eso).

Hay que tener paciecia con sencha, a un par de días de trabajo, tutoriales y guías, sigo con gusto a poco, el manejo de datos que he logrado hacer ha sido casi hackenado el sistema, realmente no entiendo este <i>framework</i> pero ya he dado los primeros pasos.

Luego de una semana y unos 5 apps de demo, varios video-tutoriales y particularmente los videos de Rohit Ghatol: http://www.youtube.com/watch?v=5F7Gx0-W-M4 y http://www.youtube.com/watch?v=0pFOrasUlOU finalmente ya me siento más cómodo con el framework y puedo lograr cosas que no salen en los ejemplos.

Links útiles:
-------------

-   <http://docs.phonegap.com/en/1.7.0/guide_getting-started_android_index.md.html> (iniciar con android y phonegap) al hacer una prueba de RUN genera automáticamente el .APK
-   <http://miamicoder.com/2012/how-to-create-a-sencha-touch-2-app-part-1/> (Este debiese haberlo leído pero no lo hice)
-   <http://stackoverflow.com/questions/9124365/image-upload-to-server-from-sencha-touch> (más links, sobre subir imágenes con phonegap, el tercero promete)
-   <http://docs.sencha.com/touch/2-0/#!/guide/native_android> (buena guía para empaquetar de android, debo replicarla aquí)
-   <http://www.mobiledevelopersolutions.com/home/download> (AppLaud plugin para Eclipse, habrá que probarla a futuro)
-   <http://docs.phonegap.com/en/2.1.0/cordova_file_file.md.html#FileTransfer> (Viene con buen ejemplo de cómo cargar una foto)

Compilar un app nativo
----------------------

Desde la carpeta de un proyecto:

<figure>
<blocquote><kbd>\
sencha package build build/production/packager.json\
</kbd></blocquote>

<figcaption>
Empaquetado de un app nativo ya minificado por sencha app build

</figcaption>
</figure>
El Sencha Touch 2 SDK 2.0.0 tiene una serie de problemas, como que no es capaz de hacer un "app build" si hay local-storage con autoload.

Con el SDK 2.0.2 que bajé si resulta.
