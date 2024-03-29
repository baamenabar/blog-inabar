title: Pie de página, datos de contacto, direcciones y microformatos.\
subtitle: Cómo aprovechar, mediante microformats, los típicos datos de contacto al pie de una página web para entregar información entendible para máquinas.\
classes: language-markup\
pubDate:2012-11-17 22:59:17

<header>
<hgroup>
Pie de página, los datos de contacto con microformato.
======================================================

Cómo usar *<span lang="en">Microformats</span>* para los datos de contacto de una persona o empresa, que están al pie de una página web, puedan ser leídos, no sólo por personas, si no que también por máquinas.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

</hgroup>
</header>
Preámbulo {#ac1}
---------

[Saltarse esta lata](#ac2)

En términos generales, los seres humanos somos asombrosamente buenos para encontrar patrones de cosas que conocemos sin importar el ruido. De casi cualquier secuencia de números o letras podemos inferir si es una dirección postal, un número de teléfono fijo o celular. Pero las máquinas necesitan ayuda para discernir si algo es una dirección de contacto o es parte de la narrativa del artículo. Hace tiempo ya que vengo dándole vueltas al tema de los datos de contacto y las innumerables maneras en las que los vemos representados.

Hace un tiempo publicamos un artículo sobre la [forma correcta de escribir una dirección postal en Chile](http://medula.cl/blog/2012/07/07/formato-de-una-direccion-postal-en-chile/) ; ahora veremos cómo hacerlas legibles para las máquinas.

*<span lang="fr">Mise en place</span>*, utensilios de trabajo {#ac2}
-------------------------------------------------------------

Como ejemplo haremos las mismas direcciones que hicimos en el artículo de direcciones postales, pero ahora haremos que una de las direcciones sea de una empresa.

Necesitaremos:

-   La [especificación de microformato para dirección](http://microformats.org/wiki/adr) como referencia.
-   La [especificación de microformato para hCard](http://microformats.org/wiki/hcard) que es la versión en microformato de **vCard** y está derivada de esa especificación.
-   Las direcciones con código postal escritas en un pie de página en <abbr title="HyperText Markup Language">HTML</abbr>

### Caso Simple

<footer>
<p>
Juan González Salinas<br>\
Coronel 2317<br>\
7510134 Providencia

</p>
<!-- otras cosas en el footer -->
</footer>
Y haremos también los datos de:

### Caso Empresa

<footer>
<h1>
Médula Diseño

</h1>
<p>
Teléfono: +56(2) 9180224

</p>
<p>
Av. Los Leones 1745, Piso 2 oficina 13 <br>\
7510838 Providencia<br>\
Santiago<br>\
Chile

</p>
<p>
<a href="mailto:info@medula.cl">info@medula.cl</a>

</p>
<!-- otras cosas en el footer -->
</footer>
Armado {#ac3}
------

Lo primero es asignar la clase `vcard` al contenedor de toda la información de contacto. Como en los ejemplos hay otras cosas en el `footer` envolveremos los datos en un `<div class="vcard">`. Algunos podrían argumentar que se debe usar `<address>` para la dirección, pero eso se puede hacer sólo si es dirección de contacto de los autores o responsables del documento. Cuando uno no está seguro de si usar un elemento o no, es mejor optar por el `div`.

### Requeridos *fn* y *n*

La propiedad **fn** es el nombre del sujeto de contacto, puede ser una persona o una organización. La propiedad **n** es el detalle de los componentes del nombre, como él título, nombre, apellido y segundo apellido.

En la práctica `fn` es la única propiedad requerida en una **hCard** si `n` se puede inferir, ya sea porque es una organización o el `fn` está compuesto por 2 palabras separadas sólo por un espacio (nombre y apellido).

### Opcionales

La parte verdaderamente práctica de una **hCard** está en el resto de los elementos. Ahora usaremos:

-   `org` que denota el nombre de una organización
-   `tel` teléfono
-   `email` dirección de email
-   `adr` la dirección que tiene las siguientes sub-propiedades:
    -   `post-office-box` Casilla Postal
    -   `street-address` Calle y número
    -   `extended-address` Departamento, block, piso, villa, etc.
    -   `locality` Ciudad
    -   `region` Comuna
    -   `postal-code` Código postal
    -   `country-name` País

La única parte complicada de definir aquí es cuando usar *<span lang="en">locality</span>* y cuando usar *<span lang="en">region</span>* dado que en Chile usamos el sistema de comunas, que es bien especial. En Chile la ciudad es irrelevante para el servicio de correos, por lo que sólo es importante la comuna. *<span lang="en">region</span>* en otras partes se usa para provincias, jefaturas, estados y divisiones similares.

Así que con esta información podremos definir bien las clases para convertir estos pedazos de información en microformatos válidos que pueden ser convertidos en una **vcard**.

### Caso Simple

<footer>
<div class="fn n">
<p>
<span class="given-name">Juan</span> <span class="family-name">González</span> <span class="additional-name">Salinas</span><br>\
<span class="adr"><span class="street-address">Coronel 2317</span><br>\
<span class="postal-code">7510134</span> <span class="region">Providencia</span></span>

</p>
</div>
<!-- otras cosas en el footer -->
</footer>
Y haremos también los datos de:

### Caso Empresa

<footer>
<div class="vcard">
<h1 class="fn org">
Médula Diseño

</h1>
<p>
Teléfono: <a class="tel" href="tel:+5629180224">+56(2) 9180224</a>

</p>
<p>
<span class="street-address">Av. Los Leones 1745</span>, <span class="extended-address">Piso 2 oficina 13</span> <br>\
<span class="postal-code">7510838</span> <span class="region">Providencia</span><br>\
<span class="locality">Santiago</span><br>\
<span class="country-name">Chile</span>

</p>
<p>
<a class="email" href="mailto:info@medula.cl">info@medula.cl</a>

</p>
</div>
<!-- otras cosas en el footer -->
</footer>
En teléfono usamos un link en vez de un `span` porque así podemos hacer que el número sea marcable desde un teléfono, usando skype u otro servicio similar. Ese tema lo abordaremos pronto en otro artículo.

Con esto la dirección es legible por una máquina y puede ser convertido automáticamente en una **vCard** o ser interpretado correctamente por buscadores como Google.

Los micro-formatos para información de contacto son mucho más completos que lo que publicamos aquí, para profundizar en más detalle sobre el tema es recomendable visitar el [wiki oficial de la especificación de hCard(Visitar la página en inglés)](http://microformats.org/wiki/hcard)

Si esto te ha sido de ayuda o me he equivocado en algo, por favor, deja tu comentario.
