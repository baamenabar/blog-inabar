title:Flujo de trabajo en Drupal con Git\
subtitle:Cómo trabajar en un proyecto de Drupal en equipo y no morir en el intento\
classes:language-php\
startDate:2014-01-27T13:35:00+00:00\
pubDate:2014-01-27T13:35:00+00:00

<header>
<hgroup>
Flujo de trabajo en Drupal con Git
==================================

Cómo trabajar en un proyecto de Drupal en equipo y no morir en el intento
-------------------------------------------------------------------------

</hgroup>
</header>
**Autor:** Nikolas Peralta Rumié - [@nikoskip](https://twitter.com/nikoskip)

Todo equipo cuenta con su flujo de trabajo establecido, lo más seguro es que hoy en día gran parte de ellos trabajen con algún sistema de control de versiones como Git o SVN, permitiendo no pisarse la cola unos a otros. Comúnmente todo funciona perfecto, ya que todo el proyecto es desarrollado por el mismo equipo, pero es un poco distinto cuando se trabaja sobre un proyecto que viene desde terceros, que no es pequeño, y que recibe actualizaciones constantemente: Drupal. Muchos tienden a colapsar cuando tienen que actualizar un módulo o el core del sistema, típicamente por problemas de incompatibilidad entre la nueva versión del core y el módulo.

Realizando una búsqueda rápida en Google sobre [flujos de trabajos en Drupal](https://www.google.cl/search?q=workflow+drupal+git), nos podemos dar cuenta que es muy similar a cualquier otro flujo de trabajo, con la excepción de ciertos puntos donde hay que tomar precauciones.

Definiendo el flujo
-------------------

![Esquema de trabajo](http://oi40.tinypic.com/96xefm.jpg "Esquema de trabajo")\
El flujo de trabajo toma en cuenta N desarrolladores que pueden estar trabajando en el proyecto, GitHub como repositorio central y 3 ramas principales:

-   **master**: como su nombre lo dice es la rama principal, aquí se encuentra el código de producción de nuestro proyecto.
-   **stage**: es una copia de **master**, donde se realizan las pruebas cuando se desarrolla una nueva funcionalidad, se instala un módulo o se edita cualquier parte del sistema. Funcionando bien en **stage**, se puede proceder a pasar a producción - **master** -.
-   **dev**: el campo de batalla, el salón de juegos. Aquí se puede hacer y deshacer sin miedo alguno. Si se instala un nuevo módulo en **dev** y todo anda de maravillas, se procede a replicar en **stage** y finalmente a **master**.

En el servidor cada rama debe ser contenida por una carpeta, por lo que si el sitio se llama **drupalhurtsme.com**, las direcciones de acceso para cada rama serían:

-   **drupalhurtsme.com/drupal**: para la rama **master**.
-   **drupalhurtsme.com/stage**: para la rama **stage**.
-   **drupalhurtsme.com/dev**: para la rama **dev**.

El nombre de los directorios es indiferente, pueden ocupar el que ustedes deseen. También pueden dejar la rama **master** en la raíz del servidor, pero para ello hay que ignorar en Git las otras dos carpetas - **stage** y **dev** en este caso - en esa rama. Es ideal que en la raíz del sitio modifiquen su archivo **robots.txt** para que las carpetas de **stage** y **dev** para que no sean rastreados por los buscadores, y se mantengan estas direcciones ocultas:

    User-agent: *
    Disallow: /dev
    Disallow: /stage

De el mismo modo que cada rama cuenta con su propia carpeta, debe contar con su propia base de datos, siendo la base de datos de **master** de la cual se replicarán las demás.

Cada desarrollador debe contar con una copia local de Drupal - copia de **master** y su base de datos - y trabajar localmente sobre una rama propia. Se podría decir que cada desarrollador cuenta con su propia rama **dev** a nivel local, la que finalmente es llevada al servidor para las pruebas pertinentes en stage. Aún así hay cambios o pruebas que se puedan desarrollar directamente en el servidor - solamente en **dev** -, ya sea subiendo los archivos por FTP o realizando ediciones vía SSH. Lo importante es hacer commit de los cambios que efectivamente se quieren mantener y descartar lo que no es necesario, de lo contrario, el equipo de trabajo se puede encontrar con problemas al momento de hacer un pull, push o merge de esa rama, provancdo que finalmente existan conflictos entre archivos.

Teniendo en cuenta esto, podemos ver que el funcionamiento de este flujo de trabajo se centra en que los archivos - módulos, themes, core, etc - viajan desde el desarrollo local, hacia **dev**, luego a **stage** y finalmente hacia **master**. A diferencia de la base de datos, que el viaje es inverso: **master** ~~&gt; **stage**~~&gt; **dev** ~~&gt; local, ya que obviamente en **master** se encuentra la base de datos actual, con datos reales y funcionando. Por lo que es clave que todas las ramas estén parejas, si **master** tiene cambios que **stage** o **dev** no tienen, se van a encontrar con problemas. A diferencia si **dev** tienes cambios que **master** no tiene, ya que obviamente luego serán reflejados allá~~ y en **stage** -.

Iniciando el repositorio
------------------------

Para partir trabajando, antes que todo en primer lugar hay que crear el repositorio en GitHub. Una vez creado, a nivel local vamos a crear la carpeta de nuestro proyecto y dentro de esa carpeta vamos a clonar nuestro repositorio, que está vacío:

    git clone git@github.com:usuario/repo.git ./

Una vez clonado y ya estando con Git inicializado en esa carpeta, procedemos a [descargar Drupal](https://drupal.org/project/drupal). Extraemos los archivos en la carpeta y hacemos commit y push de los archivos:

    git add -A
    git commit -m "Initial commit: Drupal"
    git push origin master

Ahora de esta rama principal vamos a crear **stage** y **dev**:

    git checkout -b stage
    git push origin stage
    git checkout -b dev
    git push origin dev

Ya tenemos las tres ramas idénticas en GitHub. Cómo último paso - me voy a saltar la instalación de Drupal, no es nada del otro mundo - quedaría en el servidor crear las carpetas correspondientes para cada rama, hacer un **git clone** y en GitHub [crear un webhook](https://help.github.com/articles/post-receive-hooks) para que el servidor haga automáticamente un pull de los archivos cada vez que un desarrollador haga un merge o un push directo a las ramas. Hay que tener en cuenta que si alguno de los desarrolladores - comunmente alguien frontend - va a trabajar de manera continua directamente en el servidor - se supone que sólo en **dev** -, es mejor no crearlo, ya que es posible que ese desarrollador pierda el trabajo realizado o tengan que lidiar con los conflictos de Git. Aún así se recomienda crearlos al menos para la rama **master**.

Importante tener en cuenta que Drupal por defecto viene con un **.gitignore** el que ignora los archivos generados por los usuarios y archivos de configuración, en los cuales se encuentran por ejemplo los datos de acceso a la base de datos. Deben sí o sí dejar ignorados los archivos de configuración - `sites/*/settings*.php` - y en cuanto a los archivos generados por los usuarios, queda a criterio del proyecto. En nuestro último proyecto la carpeta \`files\` contenía más de 8.000 archivos, los cuales finalmente los replicábamos manualmente cuando era necesario, ya que no eran pieza clave del desarrollo.

En nuestro último proyecto, este fue nuestro **.gitignore**:

bc.. \# Ignore configuration files that may contain sensitive information.\
sites/\*/settings\*.php

1.  Ignore paths that contain user-generated content.\
    sites/\*/files\
    sites/\*/private

\#\#\#\#\#\#\#\#\#\#\#\#\#\#

1.  Hasta aquí, es el .gitignore por defecto de Drupal\
    \#\#\#\#\#\#\#\#\#\#\#\#\#\#

<!-- -->

1.  Ignore dev files\
    /assets

/.sass-cache\
sftp-config.json

1.  Archivos de configuración del proyecto en Sublime Text\
    project.sublime-project\
    project.sublime-workspace -

\#sass-cache\
**.sass-cache**

Flujo pre-lanzamiento del proyecto
----------------------------------

Ya tenemos el repositorio creado, a este punto ya debería estar Drupal instalado y corriendo en el servidor en todas las ramas, y cada rama con su base de datos distinta. **Importante sólo instalar y no configurar nada**.

Ahora ya es momento de empezar a desarrollar, y para ello, cada desarrollador debe clonar la rama **dev** - aunque todas estén iguales - y hacer un dump de la base de datos de esta rama en el servidor para luego importarla localmente:

    mysqldump -u usuario -p nombre_base_de_datos > db.sql

Les solicitará la contraseña y el dump se encontrará en el archivo \`db.sql\`el cual deben descargar para su posterior importación local. Si no son muy amigos de la consola, esto perfectamente lo pueden hacer desde phpMyAdmin.

Si tienen acceso remoto al servidor MySQL, pueden ejecutar el comando localmente:

    mysqldump -h ip_del_servidor -u usuario -p nombre_base_de_datos > db.sql

Para importa la base de datos, en primer lugar la deben tener creada en MySQL y luego importarla:

    mysql -u usuario -p nombre_base_de_datos < db.sql

Puede que la importación tome su tiempo, las base de datos de Drupal son grandes y van ganando tamaño con el tiempo, en nuestro último proyecto la base de datos llegó a tener alrededor de 320 tablas con un peso cercano a los 25MB.

Finalmente, deben editar el archivo de configuración de Drupal en `sites/default/settings.php` con los datos correspondientes y probar que Drupal esté corriendo.

Con Drupal funcionando, ya es tiempo de desarrollar. Ya tenemos clonado el repositorio, sólo falta crear nuestra propia rama. Se supone que cada desarrollador tiene una tarea dentro del sistema, por lo que cada desarrollador trabajará sobre su propia rama, digamos que mi tarea es arreglar el diseño del sitio:

    git checkout -b diseno

Con la rama creada sólo queda empezar a trabajar, y una vez que ya se tengan avances concretos, se debe hacer el push a GitHub para luego hacer el merge correspondiente en **dev** y probar los cambios en el servidor - se entiende que saben trabajar en GItHub y hacer un **Pull request** -.

Una vez hecho el merge en **dev**, si configuraron correctamente el Webhook, automáticamente deberían ver los cambios propagados al servidor, de lo contrario, tendrán que entrar vía SSH y hacer el pull en la carpeta **/dev**. Una vez probado los cambios en **dev** es tiempo de hacer el merge en **stage**. En **stage** se deben hacer pruebas más exhaustivas, debido a que es nuestro clon del sistema en producción, y cuando hablo de clon no solamente me refiero a clon de archivos, si no que se incluye la base de datos. Teniendo en cuenta que estamos en **pre-lanzamiento** se supone que el sitio en producción y **stage** se encuentran intactos y **nadie ha instalado o modificado nada en absoluto**. Teniendo en cuenta eso, se puede ocupar **stage** como el sitio que luego será llevado a producción, incluyendo su base de datos, un clon completo.

Todo el equipo de trabajo debe funcionar de la misma manera, cada uno desarrollando sobre su propia rama local, para luego hacer push a GitHub, hacer el merge con **dev**, probar que todo esté bien en el servidor, luego el merge con **stage** y finalmente hacer el gran merge con **master** y llevar la base de datos de **stage** a **master**.

Como comentaba anteriormente es posible que ciertas modificaciones, por un tema de rapidez, se realicen directamente en el servidor - quizás un **hotfix** -. Es muy importante que esos cambios queden reflejados en el repositorio con su commit y push correspondiente, y si se hacen en **master** propagarlos a las otras dos ramas lo antes posible, de lo contrario ese cambio que se hizo lo más seguro es que se pierda en un futuro merge. Por otro lado, si el cambio se hizo en **dev**, no hay apuro en reflejarlo en las otras dos ramas.

Flujo post-lanzamiento del proyecto
-----------------------------------

En este punto, el sitio ya está en producción, cuenta con visitas, usuarios y contenido generado - imagenes, caché, etc ~~. El flujo se centra en intentar siempre trabajar con un clon del sitio en producción~~ lo más reciente posible -, a diferencia del caso anterior donde se desarrolla el proyecto y la mayoría del tiempo sólo se realizaban **push** de código hacia GitHub y luego al servidor.

Para poder estar al día con lo que se encuentra en producción, es importante poder mover esa base de datos a las otras dos ramas. En nuestro equipo hemos desarrollado un simple script PHP el cual permite tomar esa base de datos y clonarla en **stage** y **dev**:

bc(language-php).. &lt;?php

/\*\*\
\* El siguiente script se dedica a copiar la base de datos desde master ~~&gt; stage~~&gt; dev\
\*/

\$sqlOriginal = 'master'; // Nombre del archivo SQL donde se respaldara 'master'\
\$dbData = array(\
'master' =&gt; array(\
'db' =&gt; 'master',\
'user' =&gt; 'root',\
'pass' =&gt; ''\
),\
'stage' =&gt; array(\
'db' =&gt; 'stage',\
'user' =&gt; 'root',\
'pass' =&gt; ''\
),\
'dev' =&gt; array(\
'db' =&gt; 'dev',\
'user' =&gt; 'root',\
'pass' =&gt; ''\
)\
);

tryConnections(\$dbData); // Probamos que todos los datos de conexion esten OK\
echo "Conexion correcta en todas las DB...\\n";\
echo "Exportando DB 'master'...\\n";\
backupDb(\$dbData, 'master'); // Exportamos la db 'master'\
echo "Importando DB a 'stage'...\\n";\
emptyDb(\$dbData, 'stage');\
importDb(\$dbData, 'stage');\
echo "Importando DB a 'dev'...\\n";\
emptyDb(\$dbData, 'dev');\
importDb(\$dbData, 'dev');\
echo "Elimando archivo SQL original...\\n";\
unlink(\$sqlOriginal.'.sql');\
echo "Done!...\\n";

function tryConnections(\$dbData){\
foreach (\$dbData as \$key =&gt; \$data) {\
try {\
\$dbh = new PDO ('mysql:host=localhost;dbname='.\$data\['db'\], \$data\['user'\], \$data\['pass'\]);\
} catch (PDOException \$e) {\
print "Error! \[DB:\$key\]: " . \$e-&gt;getMessage() . "\\n";\
die();\
}\
}\
}

function backupDb(\$dbData, \$db){\
global \$sqlOriginal;\
\$dbData = \$dbData\[\$db\];\
exec('mysqldump --opt --password=\\''.\$dbData\['pass'\].'\\' --user='.\$dbData\['user'\].' '.\$dbData\['db'\].' &gt; '.\$sqlOriginal.'.sql');\
}

function importDb(\$dbData, \$db){\
global \$sqlOriginal;\
\$dbData = \$dbData\[\$db\];\
exec('mysql --password=\\''.\$dbData\['pass'\].'\\' --user='.\$dbData\['user'\].' '.\$dbData\['db'\].' &lt; '.\$sqlOriginal.'.sql');\
}

function emptyDb(\$dbData, \$db){\
global \$sqlOriginal;\
\$dbData = \$dbData\[\$db\];\
exec('mysql --password=\\''.\$dbData\['pass'\].'\\' --user='.\$dbData\['user'\].' -e \\'drop database '.\$dbData\['db'\].'\\';');\
exec('mysql --password=\\''.\$dbData\['pass'\].'\\' --user='.\$dbData\['user'\].' -e \\'create database '.\$dbData\['db'\].'\\';');\
}

Se recomienda que ese script se encuentre fuera del directorio web público y se ejecute exclusivamente vía consola: \`php nombre\_archivo.php\` - por temas de seguridad -. Hay que tener mucho cuidado en cuando correr el script. Si alguien del equipo está trabajando en **dev** o **stage** probando configuraciones o instalando módulos va a perder todo su trabajo y tendrá que empezar de nuevo. Por lo que debe existir consenso de cuando se desea **sincronizar** el ambiente de las ramas. El script no copia los archivos generados en producción, por lo que es posible que existan imágenes rotas u otros archivos que no van a existir. No es grave y tampoco es esencial contar con esos archivos, pero si se llegasen a requerir:

    // Estando dentro del directorio 'master', en este caso '/drupal'
    cp -r sites/default/files ../stage/sites/default
    cp -r sites/default/files ../dev/sites/default

Con eso logramos un clon total del sitio en producción en las otras ramas... por lo menos en el lado del servidor.

A nivel local es parecido, pero en vez de importar la base de datos en producción, se recomienda trabajar con la base de datos de la rama **dev**, que por así decirlo está más al día que la de producción respecto a configuraciones o módulos que tus compañeros han instalado. Por esto es importante que periódicamente - no quiero decir todos los días - sincronicen las bases de datos en el servidor, de ese modo al importar a nivel local, trabajen con un ambiente lo más parecido posible a producción.

Respecto a lo que código se trata, siempre las nuevas ramas deben salir desde **dev**, y si llevan mucho tiempo trabajando en una rama, no estaría de más decir que es recomendable hacer un **checkout** a **dev**, hacer un pull de lo que está en el repositorio, volver a tu rama de trabajo y hacer un merge con **dev**. De ese modo quedas al día respecto a lo que están haciendo tus compañeros:

    // Trabajando en rama funcionalidad_nueva
    git checkout dev
    git pull
    git checkout funcionalidad_nueva
    git merge dev

Respecto a los archivos generados por el sitio si lo desean pueden zipear - ¿existe esta palabra? - la carpeta **sites/default/files** de producción, descargarla e integrarla a su ambiente local. Pero recuerden, comúnmente no son necesarios.

Cuando hayan terminado una funcionalidad o cualquier tipo de modificación, al igual que expliqué más arriba lo primero es hacer push a GitHub, hacer el merge con **dev** en GitHub y hacer las primeras pruebas en el servidor. ¿Todo funciona bien?, perfecto, ahora toca probar en **stage**. Pero no es tan simple como hacer nuevamente un merge de **dev** a **stage** en GitHub, deben sí o sí copiar la base de datos de producción a **stage**. Es la única forma de asegurarse que los cambios que están probando efectivamente van a funcionar en producción. Y para ello pueden utilizar el script que puse más arriba, pero recuerden que ese script va a copiar la base de datos también a **dev**, ¿desean que sólo copie a **stage**?: tarea para la casa ;). Es cosa de modificar un poco el script de arriba para que [pueda recibir parámetros](http://www.php.net/manual/en/reserved.variables.argv.php) y cambiar el flujo.

Entonces, cuando el sitio está en producción lo importante es la sincronización, mantenerse al día, hacer pull seguido de la rama **dev** - de las tres ramas idealmente -, push a GitHub de tu rama, merge con **dev**, probar en el servidor, merge con **stage**, probar muy bien en esa rama y finalmente se termina con el merge a **master**. Mmmm.. pero algo falta, ¿no?.

Drupal guarda todas las configuraciones en la base de datos, ¿qué pasa si localmente instalan un módulo y lo configuran?. Lamentablemente no pueden mover esa base de datos al servidor - ni en sus sueños -, por lo que hay dos opciones:

-   Son ordenados y todos los cambios de configuración que realizaron para que el módulo que crearon o instalaron funcionen los dejaron anotados para luego replicar los mismos pasos en el servidor
-   Utilizan [Features](https://drupal.org/documentation/modules/features), un módulo que permite traspasar esas configuraciones a código, para luego poder importarlas sin mayor problema.

Nosotros fuimos ordenados :) - no fue tan tremendo -.

Actualización de Drupal y módulos
---------------------------------

Las actualizaciones de Drupal y de los módulos pueden dar un poco de miedo, debido a que pueden perder compatibilidad y aparezcan ciertos problemas en el sitio. Es por esto que **nunca**, pero **nunca** actualicen Drupal o sus módulos en el servidor. Todo se hace local.

Simplemente creen una nueva rama desde **dev**, quizás con el nombre de la versión de Drupal o del módulo, descarguen la última versión disponible, remplacen los archivos y corran en el navegador el archivo **/update.php**. Si todo sale bien, la historia es la misma, push a GitHub, merge con **dev**, etc. Cuando ya tengan los archivos actualizados en producción, ponen en modo de mantenimiento el sitio - muy importante - y corren el **/update.php**.

Como recomendación no remplacen el **.gitignore** que viene en la descarga de Drupal, ya que si ustedes lo modificaron con más reglas, el de Drupal lo pisará y Git empezará a rastrear archivos que no quieren. Misma recomendación con el **.htaccess**, si el de ustedes tiene modificaciones, tengan cuidado.

Conclusiones
------------

Como pudieron darse cuenta el flujo de trabajo no es nada de otro mundo, sólo deben recordar que todo tiene su orden y salirse de ese orden puede provocar un caos en todo el equipo de trabajo.

Para cerrar, hay algunas recomendaciones y precauciones a tener en cuenta:

-   La comunicación entre el equipo de trabajo es muy importante. Saber lo que están haciendo los demás es clave para por ejemplo no pasar a llevar pruebas en progreso en el servidor.
-   El orden en tu código y tus ramas trae consigo excelentes frutos, y habla bien de ti.
-   Por favor no intenten **hackear** el core o algún módulo de terceros que no haya sido desarrollado por ustedes o por el equipo. Esto finalmente trae más problemas que beneficios cuando tienen que actualizar el core por problemas de seguridad. Intenten siempre solucionar problemas con las herramientas que les entrega Drupal.
-   Drupal siempre intentará hacerlos sufrir, sean fuertes y piensen en sus seres queridos.
