title: Propuesta de Normas de desarrollo web\
subtitle: Criterios y normas generales, no absolutas, de buenas prácticas para el desarrollo web en Médula Diseño\
classes: language-markup\
pubDate:2012-10-02 18:06:53

Propuesta de Normas de desarrollo
=================================

En Médula Diseño estamos definiendo poíticas y estándares de desarrollo para nuestros proyectos, desde cosas tan simples como cuerpos mínimos de texto, hasta criterios de selección para frameworks de trabajo o pre-procesadores de css. Nada de esto está escrito en piedra, son guías generales hasta que haya una buena razón para romperlas.

Boilerplate:
------------

Por defecto usaremos el [boileplate de Médula Diseño](https://github.com/baamenabar/HTML5-ReMo-Boil) que viene configurado para desarrollo

-   humans.txt debe ir en UTF-8 sin BOM
-   robots.txt debe partir ocultando la carpeta /temp

*<span lang="en">Responsive</span>*:
------------------------------------

Aunque no todos los proyectos serán "mobile first" en alguna medida deben considerar usabilidad para smartphones, en general no consideramos navegadores móviles antiguos, como el de blackberry OS 4.x

SASS, COMPASS y LESS
--------------------

1.  De preferencia SASS o COMPASS con mesura.
2.  Deberemos investigar OOCSS para ser aplicado en pre-procesadores.

Concatenación y Compresión de CSS y JS
--------------------------------------

-   En CSS OK
-   JS siempre minificado, no comprimido, no usar `eval()`, usar primariamente CDNs externas
-   Concatenar JS sólo para scripts que se usan a lo largo de la página, si no la carga de la primera página es brutal.

GIT
---

Usar en la medida que se pueda.

Formatos de imágen
------------------

-   JPG a 90 de calidad
-   PNG 24 cuando se necesite transparencia o sean grandes áreas del mismo color.
-   PNG 8 para alta compresión sin pérdida de colores ni ruido.
-   GIF no usar, a menos que necesitemos GIFs animados.

CSS
---

-   NUNCA usar !important, es muy difícil de ver herencia en el inspector cuando hay important.
-   En lo posible, para anchos, márgenes y padding usar porcentaje;
-   La definición font-family SIEMPRE con un font stack, no puede ser que una web-font vaya sola, sin fallback.

HTML / Markup
-------------

\* El logotipo o logo de un sitio web no es el título (no va dentro `<h1></h1>`)

\* El logotipo debe ir en el DOM (no puede ser un DIV vacío con una imagen de fondo)

\* `label` no se usa fuera de un `<form></form>`

\* `aside` es contenido lateral, anexo al contenido principal, no porque un texto esté a la izquierda de una foto es `aside`.

<!--### (como en las fichas de abogados).-->
Diseño
------

-   Respetar los cuerpos de texto definidos en los documentos de diseño, no son por que sí. <!--En LOMG en el menú y el pie de página estaban a 14 y no 16.-->
-   Si las maquetas en Illustrator o Photoshop presentan puntos de quiebre en la diagramación, el Maquetado debe hacerse fluido entre estos puntos.
