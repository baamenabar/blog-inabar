title: Git + github + Service Hooks = renovado flujo de trabajo en PHP\
subtitle: Reemplazando el uso de FTP por Git + github …o Bitbucket\
pubDate:2012-10-02 17:13:42

<header>
<hgroup>
Git + github + Service Hooks = renovado flujo de trabajo en PHP
===============================================================

Reemplazando el uso de FTP por Git + github
-------------------------------------------

### …o Bitbucket

</hgroup>
aaaa-mm-dd: 2012-05-28 | Por: Agutín Amenabar

</header>
Finalmente me he sentado a aprender cómo usar Git y particularmente cómo conectar git a mi servidor, para que se vayan publicando inmediatamente los cambios hechos.

La verdad me ha tomado varios intentos y fracasos.

La mayor parte la logré siguiendo el tutorial\
<a href="http://net.tutsplus.com/tutorials/other/the-perfect-workflow-with-git-github-and-ssh/" title="visitar el artículo" target="_blank">The Perfect Workflow, with Git, GitHub, and SSH</a>\
de net tut+ que tiene hasta un video.

Sólo lo he traducido y he agregado algunas cosas que me complicaron al seguir el tutorial.

Hace tiempo tengo una cuenta en github, pero más que nada la usaba para estar pendiente de algunos proyectos y reportar <i>bugs</i>\
, lo mismo con la línea de comando y SSH, siempre con un ojo en el tutorial y el otro en la consola. Pero hace un par de días estoy batallando con las múltiples cosas que hay que echar a andar y finalmente lo he logrado, no fue tan terrible como pensé originalmente, dado que Chris Coyier había comentado lo complicado que era hacer esto.

Debo aclarar que soy un diseñador, que hasta hace un mes no tenía idea sobre:

-   SSH
-   Línea de comando
-   PuTTY
-   GIT

Y aunque aún entiendo muy poco de esas cosas esotéricas, han pasado a ser parte de mi flujo de trabajo y no entiendo cómo vivía sin ellos. Todo es mucho más rápido y lo mejorde todo es que son gratis!

Voy a hacer el ejemplo con el código de este mismo blog mientras lo construyo.

Paso por paso.

Repositorio local
-----------------

OK, asumiendo que ya está GIT instalado, corriendo localmente (con *Tortoise* en mi caso) y tenemos una cuenta Github, hacemos un nuevo repositorio en la carpeta del sitio, agregamos los archivos del sitio y hacemos el primer commit.

<figure>
<pre>
<kbd>\
cd carpeta/de/proyecto\
git init\
git add .\
git commit -m 'primer commit'

</kbd>

</pre>
<figcaption>
Cómo hacerlo desde la línea de comando.

</figcaption>
</figure>
<figure>
<img src="img/content/git-workflow-01.png" alt="Menú contextual, Crear repositorio aquí">

<figcaption>
Nuevo repositorio con tortoise.

</figcaption>
</figure>
Subirlo a github
----------------

Ahora a hacer un nuevo repositorio en github.

<figure>
<img src="img/content/git-workflow-03.png">

<figcaption>
Clic en "nuevo Repositorio"

</figcaption>
</figure>
<figure>
<img src="img/content/git-workflow-04.png">

<figcaption>
Se llena el simple formulario y se crea el proyecto.

</figcaption>
</figure>
En la página que aparece luego sale la dirección HTTP del proyecto, hay que buscar la dirección SSH.

<figure>
<img src="img/content/git-workflow-05.png">

<figcaption>
El formato de la dirección es: git@github.com:nombreUsuario/nombreProyecto.git

</figcaption>
</figure>
Esta dirección es útil porque nos permitirá hacer push desde nuestro repositorio local, como siempre se puede hacer de 2 maneras:

### SSH

<figure>
<pre>
<kbd>\
git remote add origin git@github.com:nombreUsuario/nombreProyecto.git\
git push -u origin master

</kbd>

</pre>
</figure>
### GUI, como TortoiseGIT

Haciendo click derecho en la carpeta del proyecto se abre TortoiseGIT &rquo; Push...

En la ventana que aparece, en el grupo <q>Destination</q> haceer click en <q class="boton">Manage</q>.

<figure>
<img src="img/content/git-workflow-06.png">

</figure>
Ojo que en el primer push hay que marcar la opción <q> <i>Force Overwrite Existing Branch (May discard changes)</i> </q> para que no entre en conflicto con la versión del repositorio que tiene github.

En la ventana que aparece hay que llenar los campos con: el nombre del proyecto, la URL de SSH que copiamos de Github y cargamos la *putty key*, navegando hasta donde esté guardada.

<figure>
<img src="img/content/git-workflow-07.png">

<figcaption>
Esta configuración es práctico guardarla apretando el botón:\
<q class="boton">Add new/Save</q>

</figcaption>
</figure>
Ahora sólo hay que apretar <q class="boton">OK</q>, probablemente va a pedir la contraseña de la llave putty.

Si revisamos en github, está todo arriba.

Actualizar el servidor desde GIT
--------------------------------

Hasta ahora no hay nada nuevo, ninguna ventaja sobre FTP (aparte del versionamiento), pero ahora haremos que el servidor cargue los archivos desde github, o sea que haga un *pull*.

### Requisitos

-   Conectarse al servidor via SSH. Personalmente uso PuTTY
-   Tener Git instalado en el servidor. Media Temple viene con Git instalado
-   <a href="http://oreilly.com/pub/h/66" target="_blank" title="Cómo autenticar un servidor con llaves ssh y sin clave.">Tener autentificado el servidor con github</a>\
    <a href="http://www.endreywalder.com/blog/authenticate-github-on-mediatemple-grid-shared-server/" title="Ver artículo de cómo hacerlo en Mediatemple">Aqí un tutorial para Media Temple</a>

### Empecemos

<figure>
<img src="img/content/git-workflow-08.png">

<figcaption>
Entrando por SSH con PuTTY

</figcaption>
</figure>
Luego navegamos hasta nuestro directorio, que en este caso es <kbd>cd domains/code.medula.cl/html</kbd>

Desde ahí podemos clonar el repositorio de github.

<figure>
<img src="img/content/git-workflow-09.png">

<figcaption>
<kbd>git clone git@github.com:nombreUsuario/nombreProyecto.git</kbd>

</figcaption>
</figure>
Toma unos segundos y ya tenemos una carpeta con el nombre del repositorio.

En este caso eso es un problema, yo quería que el index quedara directo en el sub-dominio `code.medula.cl` y no dentro de la carpeta `code.medula.cl/codeBlog `.

Así que debo borrar recursivamente la carpeta que se creó, para eso usamos el siguiente comando:

<figure>
<pre>
<kbd>rm -r -f nombreProyecto</kbd>

</pre>
<figcaption>
En mi caso escribo:\
<q>\
<kbd>rm -r -f codeBlog</kbd>\
</q>

</figcaption>
</figure>
Al hacer una búsqueda en Google por “git clone empty directory&rdquo; llegué a un grupo de Google donde se toca el tema, y <b>Lee Henson</b> [da la respuesta que necesitaba](https://groups.google.com/group/github/browse_thread/thread/1c9a5d6c823607aa?pli=1#msg_99022fb5dd2b2bb4)

<figure>
<img src="img/content/git-workflow-10.png">

<figcaption>
<kbd>git clone git@github.com:nombreUsuario/nombreProyecto.git ./</kbd>

</figcaption>
</figure>
Al parecer el comando <kbd>git clone</kbd> acepta un segundo parámetro que es la dirección donde clonar (estoy suponiendo).

De aquí en adelante no hay que hacer <kbd>git clone git@github...</kbd> si no que usaremos <kbd>git pull</kbd>

Automatizar
-----------

Todo esto está muy bien, pero sigue siendo bastante más aparatoso que FTP ¿no?

Bueno, aquí viene la magia, vamos a automatizar el proceso para que cada vez que hagamos un push a Github queden inmediatamente publicados los cambios en el servidor con el sitio.

### En github

En la página del proyecto en github, hay que apretar el botón admin.

<figure>
<img src="img/content/git-workflow-11.png">

<figcaption>
Este botón nos llevará a la página de admin del proyecto.

</figcaption>
</figure>
En la página de admin hay que apretar <strong>Service Hooks</strong>.

<figure>
<img src="img/content/git-workflow-12.png">

<figcaption>
En el campo URL hay que poner la dirección del archivo que ejecutará el\
<i>pull</i>\
<q>\
<kbd>http://code.medula.cl/gitpull.php</kbd>\
</q>\
(no lo hemos hecho aún, pero le inventamos el nombre ahora)

</figcaption>
</figure>
Esto hará que Github envíe un POST a la dirección escrita ahí, en el post irá info asociada al push, pero en este caso no nos interesa.

Apretamos\
<q class="boton">Update Settings</q>\
y estamos con github.

### En Bitbucket

En la página del repositorio ir a la pestaña\
<q class="boton">Admin</q>\
, apretar *Services* a la izquierda y en el select que aparece, seleccionar *POST* de la lista, y apretar\
<q class="boton">Add Service</q>\
donde aparecerá un campo para llenar con una URL, se usa la misma que usaríamos en github\
<q>\
<kbd>http://code.medula.cl/gitpull.php</kbd>\
</q>\
.

### El archivo PHP

Ahora, en la raíz del proyecto haremos un nuevo archivo\
<q>\
<code>gitpull.php</code>\
</q>\
y lo agregamos a git.

<figure>
<img src="img/content/git-workflow-13.png">

<figcaption>
Los iconitos delatan que aún tengo a Dreamweaver como editor predeterminado para PHP :\$

</figcaption>
</figure>
Abrimos el archivo y escribimos lo siguiente (dentro de un tag de php):

<figure>
<code class="language-php">\
exec('git pull');\
</code>

<figcaption>
Si, eso es todo.

</figcaption>
</figure>
. *EDIT: hasta el monento no me ha resultado hacer esto automatizado en Dreamhost*

. *EDIT 2: Ya lo he logrado! Era porque estaba usando tildes graves en vez de <q><code>exec()</code></q>; por alguna razón a Dreamhost no le gustaba el comando con tildes graves*

Se hace un nuevo commit y push a Github.

Entramos una última vez al servidor por SSH, navegamos hasta la carpeta y hacemos un último\
<q>\
<kbd>git pull</kbd>\
</q>

En mi caso el servidor me pide el\
<i>pass phrase</i>\
de la llave, pero igual funciona automáticamente, creo que es porque tengo otra llave sin clave para Github (eso lo veré en otro artículo).

Y eso es todo, ya podemos ver los cambios reflejados en este sitio a penas hago un push a Github o Bitbucket.

<h2>
Fuentes:

</h2>
<ul>
<li>
http://net.tutsplus.com/tutorials/other/the-perfect-workflow-with-git-github-and-ssh/ (+comment from Todd)

</li>
<li>
http://www.endreywalder.com/blog/authenticate-github-on-mediatemple-grid-shared-server/

</li>
<li>
http://douglasjarquin.com/post/1273916314/git-on-media-temple

</li>
<li>
http://blog.newgoldleaf.com/post/187406428/git-on-mediatemple (con (gs))

</li>
<li>
http://www.geekgumbo.com/2010/05/16/removing-deleted-files-from-your-git-working-directory/ (sacar arhivos eliminados)//git add -A && git add -u

</li>
</ul>
