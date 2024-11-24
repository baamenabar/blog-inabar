title:Guías de estilo front-end en sitios existentes\
subtitle: Una aproximación a generar guías de estilo en sitios web existentes\
classes: language-markup\
startDate:2014-03-20T13:40:34+00:00\
pubDate:2014-04-25T02:28:12+00:00

<header>
<hgroup>
Guías de estilo *<span lang="en">front-end</span>* para sitios existentes
=========================================================================

Una auditoría de código CSS en un sitio web antes de continuar construyendo.
----------------------------------------------------------------------------

</hgroup>
</header>
Desde que trabajamos con la [Paz Vial](http://pazvial.cl) , todos los años hacemos cambios en el sitio, este año estamos trabajando en grandes cambios, pero tenemos un legado de 3 años de modificaciones al CSS y entremedio adoptamos SASS, hay que hacer orden y limpieza, especialmente documentando los estilos de la marca. Acabo de terminarme la “Guía de bolsillo” *Front-end Style Guides* de [Anna Debenham](http://maban.co.uk/) Es una confirmación de algo que sospechaba hace tiempo. Además da un montón de buenos recursos (mencionaré un par aquí).

Lo primero es hacer una documento HTML que usaremos para poner todos los elementos del sitio dentro y ver cómo se comportan solos.

Inicialmente usamos el mismo markup del resto del sitio, cargamos los mismos .CSS y .JS.

Luego ponemos un documento base, con los típicos contenidos de un sitio, agregamos los elementos complejos que se han creado para el sitio en cuestión. Ideal es generar esta hoja de contenidos con el markup y contenido que ya existe en la página. Pero aquí pongo contenido HTML que usamos como base para generar hojas de estilo.

bc{max-height:400px;}.

<h1>
Título H1 <a href="#">con link</a>

</h1>
<p>
Un corto párrafo de texto justo debajo del encabezado H1. Debemos agregar más texto y contenido para descubrir hasta donde llega el ancho de la caja del párrafo, es paráctico hacerlo también para los encabezados y bajadas de texto. Debemos enunciar un <a href="#">link o ancla</a> dentro del párrafo e idealmente hacer.

</p>
<p>
Un segundo párrafo para hacernos una clara idea de los márgenes entre párrafos y con el siguiente encabezado. Hay gente que gusta de dar estilo al elemento <code>

<section>
</code> pero prefiero usar este espacio para difinir los elementos que van dentro del párrafo, como <small>small o pequeño</small>, <strong>strong</strong>, <b>b (bold)</b>, <em>em (emphasis)</em>, <i>i (italics o cusrivas)</i>.

</p>
<hr>
<p>
Entre el párrafo anterior y este hay un elemento <code>

<hr>
</code>

</p>
<h2>
Subtítulo H2 <a href="#">con link</a>

</h2>
<p>
Otro párrafo para hablar sobre otros elementos como listas, que a su vez pueden listar otros elementos:

<p>
<ul>
<li>
<code><q></code>: <q>Ser o no ser...</q>

</li>
<li>
<code><kbd></code>: <kbd>cd format c:</kbd>

</li>
<li>
<code><samp></code>: <samp>Are you sure you want to format C? It will delete all files and folders.</samp>

</li>
<li>
<code><code></code>: <code>line-height:2.25em;</code>

</li>
<li>
<code><code class="language-css"></code>: <code class="language-css">line-height:2.25em;</code>

</li>
</ul>
<h3>
Subtítulo H3 <a href="#">con link</a>

</h3>
<p>
El siguiente párrafo puede tratar asuntos como la combinación de elementos en línea como <strong><em>strong&gt;em destacado con énfasis, pero semánticamente lo encuentro un poco redundante</em></strong> en cambio <b><i>b&gt;i —bold con itálicas—</i></b> o <i><b>i&gt;b —itálicas con bold—</b></i> que se ven iguales, pero tienen significados semántigos distintos.

</p>
<p>
A continuación un <code>

<blockquote>
</code>

</p>
<blockquote>
<p>
The picture element is intended to give authors a way to control which image resource a user agent presents to a user, based on media query and/or support for a particular image format.

</p>
</blockquote>
<h4>
Subtítulo H4 <a href="#">con link</a>

</h4>
<p>
¿Por qué no usar lorem Ipsum? Porque diseñar sin contenido es un ejercicio incompleto. Lorem ipsum es como un bastón para alguien que no lo necesita y no corre porque tiene el bastón. Usar Lorem ipsum es un parche, que ni siquiera toma en consideración los caracteres especiales del español.

</p>
<h5>
Subtítulo H5 <a href="#">con link</a>

</h5>
<p>
Desde hace algún tiempo ya, hemos ido eliminando completamente el uso de <i>Lorem Ipsum</i>, porque termina ocultando problemas de contenido. Muchas veces, entre el cliente y uno mismo nos conformamos con esta muletilla e ignoramos la falta del contenido, muchas veces, haste que ya es demasiado tarde. Ha pasado que ni siquiera existe el contenido que se suponía iba en el lugar llenado con texto simulado.

</p>
<h6>
Subtítulo H6 <a href="#">con link</a>

</h6>
<p>
Aunque se puede argumentar que este mismo ejercicio es una contradicción de lo anterior. Porque estamos inventando párrafos y texto que no es contenido final, con el sólo propósito de testear los estilos gráficos de una manera genérica.

</p>
<h2>
Imágenes

</h2>
<h3>
Imagen sola

</h3>
<img src="img/content/el-rey-abejo-lego.jpg" alt="Figura de lego de personaje, El Rey Abejo">

<h3>
Imágen en una figura <code>

<figure>
</code>

</h3>
<figure>
<img src="img/content/el-rey-abejo-dibujo.jpg" alt="Dibujo de personaje con ropa amarilla y un látigo, el Rey Abejo">

<figcaption>
El Rey Abejo demuestra el uso de su látigo bandeado con punta de rubí.

</figcaption>
</figure>
<h3>
Imagen en línea

</h3>
<p>
Aquí hay una imagen de la Amanita muscaria, también conocido como matamoscas o falsa oronja. Las imágenes en línea, deben estar alineadas verticalmente a la línea base o <code class="language-css">vertical-aign:baseline;</code> Así queda alineada a la base de la línea de texto donde se encuentra incrustada. Las imágenes en línea <img src="img/content/casa-pitufos.png" alt="Dos hongos amanita muscaria"> sí aceptan valores para <code class="language-css">margin-top</code> y <code class="language-css">margin-bottom</code>, a diferencia de los <span>span</span>.

</p>
<h2>
Listas

</h2>
<h3>
Lista ordenada <code>

<ol>
</code>

</h3>
<ol>
<li>
Primer ítem

</li>
<li>
Segundo ítem <a href="#">con link</a>

</li>
<li>
Tercer ítem <em>con énfasis</em>

</li>
<li>
Cuarto ítem <strong>con fuerte énfasis</strong>

</li>
<li>
El quinto ítem tiene una sub-lista

<ol>
<li>
Uno

</li>
<li>
Dos

</li>
<li>
Tres

</li>
<li>
Cuatro

</li>
</ol>
</li>
</ol>
<h3>
Lista sin orden <code>

<ul>
</code>

</h3>
<ul>
<li>
Primer ítem

</li>
<li>
Segundo ítem <a href="#">con link</a>

</li>
<li>
Tercer ítem <em>con énfasis</em>

</li>
<li>
Cuarto ítem <strong>con fuerte énfasis</strong>

</li>
<li>
El quinto ítem tiene una sub-lista

<ul>
<li>
Uno

</li>
<li>
Dos

</li>
<li>
Tres

</li>
<li>
Cuatro

</li>
</ul>
</li>
</ul>
<h3>
Lista con bloques dentro de cada elemento.

</h3>
<ul>
<li>
<code>

<li>
</li>
</code>La factura electrónica es un documento digital legalmente válido como medio de respaldo de las operaciones comerciales entre contribuyentes y, por lo tanto, reemplaza a las facturas tradicionales de papel. El ser facturador electrónico habilita al contribuyente como emisor y receptor de Documentos Tributarios Electrónicos (DTE) como por ejemplo: factura electrónica, nota de crédito, nota de débito y guía de despacho.

</li>
<li>
<p>
<code>

<li>
<p>
</p>
</li>
</code>Sus costos dependerán del precio del sistema que le ofrece la empresa proveedora y de su implementación. Más los costos técnicos y de operación, como por ejemplo: compra de Certificado Digital, Internet, Computador e Impresora.

</p>
</li>
<li>
<p>
<code>

<li>
<p>
</p>
<p>
</p>
<p>
</p>
</li>
</code>

</p>
<p>
Pueden emitir facturas, notas de crédito y débito, guía de despacho, factura de compra, de exportación, notas de crédito y débito de exportación, liquidación factura , boletas afectas, ente otros.

</p>
<p>
El Sistema no tiene costos, excepto los asociados a los requisitos técnicos y de operación, como por ejemplo: compra de Certificado Digital, Internet, Computador e Impresora.

</p>
</li>
<li>
<img src="img/content/huinganal-con-nieve.jpg" alt="Fotografía de cerro nevado con ciclista subiendo y cerro manquehue a la distancia">

<p>
<code>

<li>
<img>

</li>
</code>

</p>
</li>
</ul>
<h2>
Formularios y sus elementos

</h2>
<form id="unoFormo" action="./">
<p>
Los elementos de un form, en general, son por defecto <code class="language-css">display:inline;</code> y <code class="language-css">display:inline-block;</code> por lo que es mejor meterlos dentro de un bloque para que no quede una majamama de elementos.

</p>
<p>
Personalmente prefiero las listas y en los estilos defino que los li dentro de un form se comportan parecido a un div.

</p>
<ul>
<li>
<label for="nombre1">Etiqueta 1: para texto</label>\
<input type="text" name="nombre1" id="nombre1">

</li>
<li>
<label for="nombre2">Etiqueta 2: para email</label>\
<input type="email" name="nombre2" id="nombre2">

</li>
<li>
<label for="nombre3">Etiqueta 3: para número</label>\
<input type="number" name="nombre3" id="nombre3">

</li>
<li>
<label>Etiqueta 4: con el input adentro <input type="text" name="nombre4" id="nombre4" placeholder="con texto placeholder"></label>

<li>
<label for="nombre5">Etiqueta 5: Búsqueda</label>\
<input type="search" name="nombre5" id="nombre5">

</li>
<li>
<label for="nombre6">Etiqueta Checkbox</label>\
<input type="checkbox" name="nombre6" id="nombre6">

</li>
<li>
<label><input type="checkbox" name="nombre7" id="nombre7"> Etiqueta Checkbox con caja dentro del <code><label></code></label>

</li>
<li>
<label>Etiqueta Checkbox con caja dentro del <code><label></code>, a la derecha: <input type="checkbox" name="nombre8" id="nombre8"></label>

</li>
<li>
<label for="nombre11"> Etiqueta antes de: </label><input type="radio" id="nombre11" name="nombreRadios">

</li>
<li>
<input type="radio" id="nombre12" name="nombreRadios"><label for="nombre12">Etiqueta después del radio</label>

</li>
<li>
<label><input type="radio" id="nombre13" name="nombreRadios"> Etiqueta Radio con caja dentro del <code><label></code></label>

</li>
<li>
<label>Etiqueta Radio con caja dentro del <code><label></code>, a la derecha: <input type="radio" id="nombre14" name="nombreRadios"></label>

</li>
<li>
<label for="elSelect">Etiqueta: Seleccione un número</label> <select name="elSelect">\
<option value="0">Seleccione...</option>\
<option value="1">Uno</option>\
<option value="2">Dos</option>\
<option value="3">Tres</option>\
</select>

</li>
<li>
<input type="button" name="nombre8" id="nombre8" value="Input botón">

<button type="button">
Button

</button>
<p>
Ojo que un button debe tener <code>type="button"</code> si no se quiere que envíe automáticamente el formulario.

</p>
</li>
<li>
A veces es útil tener estilos para que <a href="#" class="button">link</a> parezca botón <code>a.button</code>

</li>
<li>
<textarea name="nombre7" id="nombre7" cols="30" rows="10">
</textarea>
</li>
</ul>
</form>
<h2>
Tablas

</h2>
<p>
Las tablas son para mostrar información tabulada, en celdas.

</p>
<table cellspacing="0">
<thead>
<tr>
<th>
Cepa

</th>
<th>
Año plantación

</th>
<th>
Has. plantadas

</th>
</tr>
</thead>
<tbody>
<tr>
<td>
Merlot

</td>
<td>
2010

</td>
<td>
10

</td>
</tr>
<tr>
<td>
Cabernet Franc

</td>
<td>
2009

</td>
<td>
7,6

</td>
</tr>
<tr>
<td>
Cabernet Sauvignon

</td>
<td>
2011

</td>
<td>
5

</td>
</tr>
<tr>
<td>
Syrah

</td>
<td>
2008

</td>
<td>
11,2

</td>
</tr>
<tr>
<td>
Malbec

</td>
<td>
2012

</td>
<td>
1

</td>
</tr>
<tr>
<td>
Carmenere

</td>
<td>
2009

</td>
<td>
7,4

</td>
</tr>
</tbody>
<tfoot>
<tr>
<td>
Totales:

</td>
<td>
</td>
<td>
42,2 Has.

</td>
</tr>
</tfoot>
</table>
<h2>
Más elementos o grupos

</h2>
<p>
Hay muchos más elementos y innumerables combinaciones de estos, la idea es ir agregando aquí los de uso más común, o específicos de cada proyecto. Algunos ejemplos:

</p>
<ul>
<li>
Paginación

</li>
<li>
Fechas <code><time></code>

</li>
<li>
Direcciones

</li>
<li>
Marcas de edición <code><mark><del><ins></code>

</li>
</ul>
Veamos cómo queda este texto con la hoja de estilos de este blog.


&lt;style&gt;
.muestraDeHtml{
padding: 1em;
margin: -1em;
background: \#f0f0f0; /\* Old browsers \*/
background: -moz-linear-gradient(left, \#f0f0f0 0%, \#ffffff 100%); /\* FF3.6+ \*/
background: -webkit-gradient(linear, left top, right top, color-stop(0%,\#f0f0f0), color-stop(100%,\#ffffff)); /\* Chrome,Safari4+ \*/
background: -webkit-linear-gradient(left, \#f0f0f0 0%,\#ffffff 100%); /\* Chrome10+,Safari5.1+ \*/
background: -o-linear-gradient(left, \#f0f0f0 0%,\#ffffff 100%); /\* Opera 11.10+ \*/
background: -ms-linear-gradient(left, \#f0f0f0 0%,\#ffffff 100%); /\* IE10+ \*/
background: linear-gradient(to right, \#f0f0f0 0%,\#ffffff 100%); /\* W3C \*/
}
&lt;/style&gt;
&lt;div class="muestraDeHtml"&gt;
&lt;h1&gt;Título H1 &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/h1&gt;
&lt;p&gt;Un corto párrafo de texto justo debajo del encabezado H1. Debemos agregar más texto y contenido para descubrir hasta donde llega el ancho de la caja del párrafo, es paráctico hacerlo también para los encabezados y bajadas de texto. Debemos enunciar un &lt;a href="\#"&gt;link o ancla&lt;/a&gt; dentro del párrafo e idealmente hacer.&lt;/p&gt;
&lt;p&gt;Un segundo párrafo para hacernos una clara idea de los márgenes entre párrafos y con el siguiente encabezado. Hay gente que gusta de dar estilo al elemento &lt;code&gt;&lt;section&gt;&lt;/code&gt; pero prefiero usar este espacio para difinir los elementos que van dentro del párrafo, como &lt;small&gt;small o pequeño&lt;/small&gt;, &lt;strong&gt;strong&lt;/strong&gt;, &lt;b&gt;b (bold)&lt;/b&gt;, &lt;em&gt;em (emphasis)&lt;/em&gt;, &lt;i&gt;i (italics o cusrivas)&lt;/i&gt;.&lt;/p&gt;
&lt;hr&gt;
&lt;p&gt;Entre el párrafo anterior y este hay un elemento &lt;code&gt;&lt;hr&gt;&lt;/code&gt;&lt;/p&gt;
&lt;h2&gt;Subtítulo H2 &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/h2&gt;
&lt;p&gt;Otro párrafo para hablar sobre otros elementos como listas, que a su vez pueden listar otros elementos:&lt;p&gt;
&lt;ul&gt;
    &lt;li&gt;&lt;code&gt;&lt;q&gt;&lt;/code&gt;: &lt;q&gt;Ser o no ser...&lt;/q&gt;&lt;/li&gt;
    &lt;li&gt;&lt;code&gt;&lt;kbd&gt;&lt;/code&gt;: &lt;kbd&gt;cd format c:&lt;/kbd&gt;&lt;/li&gt;
    &lt;li&gt;&lt;code&gt;&lt;samp&gt;&lt;/code&gt;: &lt;samp&gt;Are you sure you want to format C? It will delete all files and folders.&lt;/samp&gt;&lt;/li&gt;
    &lt;li&gt;&lt;code&gt;&lt;code&gt;&lt;/code&gt;: &lt;code&gt;line-height:2.25em;&lt;/code&gt;&lt;/li&gt;
    &lt;li&gt;&lt;code&gt;&lt;code class="language-css"&gt;&lt;/code&gt;: &lt;code class="language-css"&gt;line-height:2.25em;&lt;/code&gt;&lt;/li&gt;
&lt;/ul&gt;
&lt;h3&gt;Subtítulo H3 &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/h3&gt;
&lt;p&gt;El siguiente párrafo puede tratar asuntos como la combinación de elementos en línea como &lt;strong&gt;&lt;em&gt;strong&gt;em destacado con énfasis, pero semánticamente lo encuentro un poco redundante&lt;/em&gt;&lt;/strong&gt; en cambio &lt;b&gt;&lt;i&gt;b&gt;i —bold con itálicas—&lt;/i&gt;&lt;/b&gt; o &lt;i&gt;&lt;b&gt;i&gt;b —itálicas con bold—&lt;/b&gt;&lt;/i&gt; que se ven iguales, pero tienen significados semántigos distintos.&lt;/p&gt;
&lt;p&gt;A continuación un &lt;code&gt;&lt;blockquote&gt;&lt;/code&gt;&lt;/p&gt;
&lt;blockquote&gt;
    &lt;p&gt;The picture element is intended to give authors a way to control which image resource a user agent presents to a user, based on media query and/or support for a particular image format.&lt;/p&gt;
&lt;/blockquote&gt;
&lt;h4&gt;Subtítulo H4 &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/h4&gt;
&lt;p&gt;¿Por qué no usar lorem Ipsum? Porque diseñar sin contenido es un ejercicio incompleto. Lorem ipsum es como un bastón para alguien que no lo necesita y no corre porque tiene el bastón. Usar Lorem ipsum es un parche, que ni siquiera toma en consideración los caracteres especiales del español.&lt;/p&gt;
&lt;h5&gt;Subtítulo H5 &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/h5&gt;
&lt;p&gt;Desde hace algún tiempo ya, hemos ido eliminando completamente el uso de &lt;i&gt;Lorem Ipsum&lt;/i&gt;, porque termina ocultando problemas de contenido. Muchas veces, entre el cliente y uno mismo nos conformamos con esta muletilla e ignoramos la falta del contenido, muchas veces, haste que ya es demasiado tarde. Ha pasado que ni siquiera existe el contenido que se suponía iba en el lugar llenado con texto simulado.&lt;/p&gt;
&lt;h6&gt;Subtítulo H6 &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/h6&gt;
&lt;p&gt;Aunque se puede argumentar que este mismo ejercicio es una contradicción de lo anterior. Porque estamos inventando párrafos y texto que no es contenido final, con el sólo propósito de testear los estilos gráficos de una manera genérica.&lt;/p&gt;
&lt;h2&gt;Imágenes&lt;/h2&gt;
&lt;h3&gt;Imagen sola&lt;/h3&gt;
&lt;img src="img/content/el-rey-abejo-lego.jpg" alt="Figura de lego de personaje, El Rey Abejo"&gt;
&lt;h3&gt;Imágen en una figura &lt;code&gt;&lt;figure&gt;&lt;/code&gt;&lt;/h3&gt;
&lt;figure&gt;
    &lt;img src="img/content/el-rey-abejo-dibujo.jpg" alt="Dibujo de personaje con ropa amarilla y un látigo, el Rey Abejo"&gt;
    &lt;figcaption&gt;El Rey Abejo demuestra el uso de su látigo bandeado con punta de rubí.&lt;/figcaption&gt;
&lt;/figure&gt;
&lt;h3&gt;Imagen en línea&lt;/h3&gt;
&lt;p&gt;Aquí hay una imagen de la Amanita muscaria, también conocido como matamoscas o falsa oronja. Las imágenes en línea, deben estar alineadas verticalmente a la línea base o &lt;code class="language-css"&gt;vertical-aign:baseline;&lt;/code&gt; Así queda alineada a la base de la línea de texto donde se encuentra incrustada. Las imágenes en línea &lt;img src="img/content/casa-pitufos.png" alt="Dos hongos amanita muscaria"&gt; sí aceptan valores para &lt;code class="language-css"&gt;margin-top&lt;/code&gt; y &lt;code class="language-css"&gt;margin-bottom&lt;/code&gt;, a diferencia de los &lt;span&gt;span&lt;/span&gt;.&lt;/p&gt;
&lt;h2&gt;Listas&lt;/h2&gt;
&lt;h3&gt;Lista ordenada &lt;code&gt;&lt;ol&gt;&lt;/code&gt;&lt;/h3&gt;
&lt;ol&gt;
    &lt;li&gt;Primer ítem&lt;/li&gt;
    &lt;li&gt;Segundo ítem &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/li&gt;
    &lt;li&gt;Tercer ítem &lt;em&gt;con énfasis&lt;/em&gt;&lt;/li&gt;
    &lt;li&gt;Cuarto ítem &lt;strong&gt;con fuerte énfasis&lt;/strong&gt;&lt;/li&gt;
    &lt;li&gt;El quinto ítem tiene una sub-lista
        &lt;ol&gt;
            &lt;li&gt;Uno&lt;/li&gt;
            &lt;li&gt;Dos&lt;/li&gt;
            &lt;li&gt;Tres&lt;/li&gt;
            &lt;li&gt;Cuatro&lt;/li&gt;
        &lt;/ol&gt;
    &lt;/li&gt;
&lt;/ol&gt;
&lt;h3&gt;Lista sin orden &lt;code&gt;&lt;ul&gt;&lt;/code&gt;&lt;/h3&gt;
&lt;ul&gt;
    &lt;li&gt;Primer ítem&lt;/li&gt;
    &lt;li&gt;Segundo ítem &lt;a href="\#"&gt;con link&lt;/a&gt;&lt;/li&gt;
    &lt;li&gt;Tercer ítem &lt;em&gt;con énfasis&lt;/em&gt;&lt;/li&gt;
    &lt;li&gt;Cuarto ítem &lt;strong&gt;con fuerte énfasis&lt;/strong&gt;&lt;/li&gt;
    &lt;li&gt;El quinto ítem tiene una sub-lista
        &lt;ul&gt;
            &lt;li&gt;Uno&lt;/li&gt;
            &lt;li&gt;Dos&lt;/li&gt;
            &lt;li&gt;Tres&lt;/li&gt;
            &lt;li&gt;Cuatro&lt;/li&gt;
        &lt;/ul&gt;
    &lt;/li&gt;
&lt;/ul&gt;
&lt;h3&gt;Lista con bloques dentro de cada elemento.&lt;/h3&gt;
&lt;ul&gt;
    &lt;li&gt;&lt;code&gt;&lt;li&gt;&lt;/li&gt;&lt;/code&gt;La factura electrónica es un documento digital legalmente válido como medio de respaldo de las operaciones comerciales entre contribuyentes y, por lo tanto, reemplaza a las facturas tradicionales de papel. El ser facturador electrónico habilita al contribuyente como emisor y receptor de Documentos Tributarios Electrónicos (DTE) como por ejemplo: factura electrónica, nota de crédito, nota de débito y guía de despacho.&lt;/li&gt;
    &lt;li&gt;&lt;p&gt;&lt;code&gt;&lt;li&gt;&lt;p&gt;&lt;/p&gt;&lt;/li&gt;&lt;/code&gt;Sus costos dependerán del precio del sistema que le ofrece la empresa proveedora y de su implementación.  Más los costos técnicos y de operación, como por ejemplo: compra de Certificado Digital, Internet,  Computador e Impresora.&lt;/p&gt;&lt;/li&gt;
    &lt;li&gt;
        &lt;p&gt;&lt;code&gt;&lt;li&gt;&lt;p&gt;&lt;/p&gt;&lt;p&gt;&lt;/p&gt;&lt;p&gt;&lt;/p&gt;&lt;/li&gt;&lt;/code&gt;&lt;/p&gt;
        &lt;p&gt;Pueden emitir facturas, notas de crédito y débito, guía de despacho, factura de compra, de exportación, notas de crédito y débito de exportación, liquidación factura , boletas afectas, ente otros.&lt;/p&gt;
        &lt;p&gt;El Sistema no tiene costos, excepto los asociados a los requisitos técnicos y de operación, como por ejemplo: compra de Certificado Digital, Internet, Computador e Impresora.&lt;/p&gt;
    &lt;/li&gt;
    &lt;li&gt;
        &lt;img src="img/content/huinganal-con-nieve.jpg" alt="Fotografía de cerro nevado con ciclista subiendo y cerro manquehue a la distancia"&gt;
        &lt;p&gt;&lt;code&gt;&lt;li&gt;&lt;img&gt;&lt;/li&gt;&lt;/code&gt;&lt;/p&gt;
    &lt;/li&gt;
&lt;/ul&gt;
&lt;h2&gt;Formularios y sus elementos&lt;/h2&gt;
&lt;form id="unoFormo" action="./"&gt;
    &lt;p&gt;Los elementos de un form, en general, son por defecto &lt;code class="language-css"&gt;display:inline;&lt;/code&gt; y &lt;code class="language-css"&gt;display:inline-block;&lt;/code&gt; por lo que es mejor meterlos dentro de un bloque para que no quede una majamama de elementos.&lt;/p&gt;
    &lt;p&gt;Personalmente prefiero las listas y en los estilos defino que los li dentro de un form se comportan parecido a un div.&lt;/p&gt;
    &lt;ul&gt;
        &lt;li&gt;&lt;label for="nombre1"&gt;Etiqueta 1: para texto&lt;/label&gt;
        &lt;input type="text" name="nombre1" id="nombre1"&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label for="nombre2"&gt;Etiqueta 2: para email&lt;/label&gt;
        &lt;input type="email" name="nombre2" id="nombre2"&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label for="nombre3"&gt;Etiqueta 3: para número&lt;/label&gt;
        &lt;input type="number" name="nombre3" id="nombre3"&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label&gt;Etiqueta 4: con el input adentro &lt;input type="text" name="nombre4" id="nombre4" placeholder="con texto placeholder"&gt;&lt;/label&gt;
        &lt;li&gt;&lt;label for="nombre5"&gt;Etiqueta 5: Búsqueda&lt;/label&gt;
        &lt;input type="search" name="nombre5" id="nombre5"&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label for="nombre6"&gt;Etiqueta Checkbox&lt;/label&gt;
        &lt;input type="checkbox" name="nombre6" id="nombre6"&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label&gt;&lt;input type="checkbox" name="nombre7" id="nombre7"&gt; Etiqueta Checkbox con caja dentro del &lt;code&gt;&lt;label&gt;&lt;/code&gt;&lt;/label&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label&gt;Etiqueta Checkbox con caja dentro del &lt;code&gt;&lt;label&gt;&lt;/code&gt;, a la derecha: &lt;input type="checkbox" name="nombre8" id="nombre8"&gt;&lt;/label&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label for="nombre11"&gt; Etiqueta antes de: &lt;/label&gt;&lt;input type="radio" id="nombre11" name="nombreRadios"&gt;&lt;/li&gt;
        &lt;li&gt;&lt;input type="radio" id="nombre12" name="nombreRadios"&gt;&lt;label for="nombre12"&gt;Etiqueta después del radio&lt;/label&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label&gt;&lt;input type="radio" id="nombre13" name="nombreRadios"&gt; Etiqueta Radio con caja dentro del &lt;code&gt;&lt;label&gt;&lt;/code&gt;&lt;/label&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label&gt;Etiqueta Radio con caja dentro del &lt;code&gt;&lt;label&gt;&lt;/code&gt;, a la derecha: &lt;input type="radio" id="nombre14" name="nombreRadios"&gt;&lt;/label&gt;&lt;/li&gt;
        &lt;li&gt;&lt;label for="elSelect"&gt;Etiqueta: Seleccione un número&lt;/label&gt; &lt;select name="elSelect"&gt;
            &lt;option value="0"&gt;Seleccione...&lt;/option&gt;
            &lt;option value="1"&gt;Uno&lt;/option&gt;
            &lt;option value="2"&gt;Dos&lt;/option&gt;
            &lt;option value="3"&gt;Tres&lt;/option&gt;
        &lt;/select&gt;&lt;/li&gt;
        &lt;li&gt;&lt;input type="button" name="nombre8" id="nombre8" value="Input botón"&gt;
        &lt;button type="button"&gt;Button&lt;/button&gt;
        &lt;p&gt;Ojo que un button debe tener &lt;code&gt;type="button"&lt;/code&gt; si no se quiere que envíe automáticamente el formulario.&lt;/p&gt;&lt;/li&gt;
        &lt;li&gt;A veces es útil tener estilos para que &lt;a href="\#" class="button"&gt;link&lt;/a&gt; parezca botón &lt;code&gt;a.button&lt;/code&gt;&lt;/li&gt;
        &lt;li&gt;&lt;textarea name="nombre7" id="nombre7" cols="30" rows="10"&gt;&lt;/textarea&gt;&lt;/li&gt;
    &lt;/ul&gt;
&lt;/form&gt;
&lt;h2&gt;Tablas&lt;/h2&gt;
&lt;p&gt;Las tablas son para mostrar información tabulada, en celdas.&lt;/p&gt;
&lt;table cellspacing="0"&gt;
    &lt;thead&gt;&lt;tr&gt;
        &lt;th&gt;Cepa&lt;/th&gt;
        &lt;th&gt;Año plantación&lt;/th&gt;
        &lt;th&gt;Has. plantadas&lt;/th&gt;
    &lt;/tr&gt;&lt;/thead&gt;
    &lt;tbody&gt;
        &lt;tr&gt;
            &lt;td&gt;Merlot&lt;/td&gt;
            &lt;td&gt;2010&lt;/td&gt;
            &lt;td&gt;10&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Cabernet Franc&lt;/td&gt;
            &lt;td&gt;2009&lt;/td&gt;
            &lt;td&gt;7,6&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Cabernet Sauvignon&lt;/td&gt;
            &lt;td&gt;2011&lt;/td&gt;
            &lt;td&gt;5&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Syrah&lt;/td&gt;
            &lt;td&gt;2008&lt;/td&gt;
            &lt;td&gt;11,2&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Malbec&lt;/td&gt;
            &lt;td&gt;2012&lt;/td&gt;
            &lt;td&gt;1&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Carmenere&lt;/td&gt;
            &lt;td&gt;2009&lt;/td&gt;
            &lt;td&gt;7,4&lt;/td&gt;
        &lt;/tr&gt;
    &lt;/tbody&gt;
    &lt;tfoot&gt;
    &lt;tr&gt;
        &lt;td&gt;Totales:&lt;/td&gt;
        &lt;td&gt;&lt;/td&gt;
        &lt;td&gt;42,2 Has.&lt;/td&gt;
    &lt;/tr&gt;
    &lt;/tfoot&gt;
&lt;/table&gt;
&lt;h2&gt;Más elementos o grupos&lt;/h2&gt;
&lt;p&gt;Hay muchos más elementos y innumerables combinaciones de estos, la idea es ir agregando aquí los de uso más común, o específicos de cada proyecto. Algunos ejemplos:&lt;/p&gt;
&lt;ul&gt;
    &lt;li&gt;Paginación&lt;/li&gt;
    &lt;li&gt;Fechas &lt;code&gt;&lt;time&gt;&lt;/code&gt;&lt;/li&gt;
    &lt;li&gt;Direcciones&lt;/li&gt;
    &lt;li&gt;Marcas de edición &lt;code&gt;&lt;mark&gt;&lt;del&gt;&lt;ins&gt;&lt;/code&gt;&lt;/li&gt;
&lt;/ul&gt;
&lt;hr&gt;
&lt;/div&gt;

Haciendo esta prueba en este blog noto varias falencias en mi hoja de estilos, hay varios usos que no contempla; pero en términos generales estoy bastante contento con lo completa que construí la hoja de estilos.

Haciendo el mismo ejercicio para el sitio de la Paz Vial, noto errores en los estilos básicos, varios de los cuales me habría ahorrado de haber usado Normalize en vez de un *\[lang=en\]reset* de\
CSS, pero no existía normalize.css en esa época. El resultado del ejercicio lo pueden ver en: <http://estilos.pazvial.cl/> .

En este caso usamos los elementos existentes en el sitio, durante el re-diseño iremos agregando correcciones y el esqueleto de aquí arriba. Una vez pegado los típicos elementos se pueden seguir agregando otras cosas que se hayan creado para el sitio, como navegación, footer y cualquier elemento que necesite tener estilos.

Otra cosa que es práctico agregar es el uso del logotipo de la marca, poner todas las versions disponibles, y si hay mútiples formatos de archivo, almenos agregar links a ellos. Es también un buen lugar para mantener los manuales de normas gráficas.

Conclusión
----------

Este documento HTML base tiene su propio repositorio en GitHub <https://github.com/MedulaDesign/guias-estilo> . Lo usamos como *\[lang=en\]boilerplate* para que al hacer los estilos no se nos quede nada en el tintero.

Tiene 3 hojas de estilo que van aumentando la complejidad, primero se aplica **normalize**, luego algunas arbitrariedades para que no se vea tan feo y ajustar algunas cosas que normalize no considera, luego tiene una tercera hoja de estilo con mañas mías y clases *\[lang=en\]helper*, cosas como estilos básicos para tablas, listas dentro de los nav o forms, un poco de color, etc.

Esto está basado principalmente en la [Guía de estilo de Paul Robert Lloyd para Barebones](http://barebones.paulrobertlloyd.com/_styleguide.php) que a su vez está basada en la [Pattern primer de Jeremy Keith](http://patternprimer.adactio.com/)
