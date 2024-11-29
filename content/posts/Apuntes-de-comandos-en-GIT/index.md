---
title: Apuntes de comandos de GIT para trabajo distribuido.
summary: "Flujo de trabajo para equipos remotos usando versionamiento de código con GIT"
classes: language-markup
lang: es
date: 2014-03-19T14:37:04+00:00
tags: ["git", "versionamiento"]
---

# Apuntes de comandos de GIT para trabajo en equipos remotos

**Autores:** B. Agustín Amenábar L.

Somos 2 personas trabajando en el código de múltiples sitios y proyectos; uno está viendo backend y el otro frontend —Aunque nos mezclamos bastante las tareas. Aquí voy poniendo apuntes de los típicos comandos que usamos mientras trabajamos usando GIT.

Actualizar mi branch con lo que hay en el master `git merge master` .

Una vez agregado SASS, si quiero evitar que .sass-cache quede versionado

```sh
git rm -r -f wp-content/themes/nombredeltheme/.sass-cache --cached
```

Lo de arriba significa:

git remueve `-recursivamente` `-fuerza` la_carpeta_y_contenidos `--pero dejando una copia en el disco`

### Nueva branch {#ac1}

Lo hacemos en Github y luego localmente sólo hacemos `git fetch` o `git pull`

O podemos hacer `git checkout -b nueva-rama` y se hace un _push_ con `git push origin nuvea-rama`

### Borrando un branch {#ac2}

Localmente: `git branch -D nombre_del_branch`

Remoto: `git push origin --delete nombre_del_branch`

### Cancelando un merge {#ac3}

Si la versión de git es mayor o igual a 1.7.4 se puede hacer `git merge --abort`

### Hacer un merge sólo un archivo {#ac4}

A veces tienes un archivo en un branch que necesitas actualizar en otro branch, por ejemplo.

En el branch `nuevo_catalogo` tienes un archivo `credenciales-db.inc` con credenciales a una nueva base de datos y necesitas actualizar esas credenciales en `master`, entonces los comandos serían los siguientes:

```sh
git checkout master
git checkout --patch nuevo_catalogo credenciales-db.inc
```

El primero es para ir al branch en el que quieres hacer la modificación, el segundo copia desde el otro branch los cambios al archivo que quieres actualizar.

Cuando aprietes <kbd>enter</kbd> mostrará un dif del cambio y preguntará: `Apply this hunk to index and worktree [y,n,q,a,d,/,s,e,?]?`

Aprienta <kbd>y</kbd> (yes) y <kbd>enter</kbd>, eso hará el cambio en el branch. Este es el significado de las primeras 4 opciones.

y - (_<span lang="en">yes</span>_) si, aplicar los cambios al arbol de trabajo de este branch\
n - (_<span lang="en">no</span>_) **no**, aplicar los cambios al arbol de trabajo de este branch\
q - (_<span lang="en">quit</span>_) salir; no aplicar cambios en este archivo ni en ninguno de los restantes (para cuando se hace un patch de varios)\
a - (_<span lang="en">apply</span>_) aplicar cambios en este archivo y en todos los restantes (para cuando se hace un patch de varios)

#### La corta {#ac5}

```sh
git checkout --theirs nuevo_catalogo credenciales-db.inc
```

Eso hace el cambio sin preguntar, esté en stage, tenga cambios o no, simplemente lo reescribe.

refs:

-   <http://stackoverflow.com/questions/10784523/how-do-i-merge-changes-to-a-single-file-rather-than-merging-commits>
-   <http://chimera.labs.oreilly.com/books/1230000000561/ch07.html#_resolving_merge_conflicts>
-   <http://git-scm.com/docs/git-format-patch>

### Eliminar todos los cambios desde el último commit {#ac6}

```sh
git checkout .
```

Solo sirve para los cambios que no han sido _<span lang="en">staged</span>_.

Para _<span lang="en">unstage</span>_ los archivos en un branch, digamos `master`, hay que escribir lo siguiente:

```sh
git reset master
```

Y luego se puede hacer `git checkout .` para devolver el branch completamente a como estaba en el útimo commit.

### Eliminar todos los archivos nuevos desde el último commit {#ac7}

```sh
git clean -d -f
```

`-d` es para directorios y `-f` es para forzarlo si no te deja hacerlo sin usar -f. Git clean está bien documentado en [La documentación de Linux para Git](https://www.kernel.org/pub/software/scm/git/docs/git-clean.html)

Igual siempre hago `git clean -f -d -n` para ver qué voy a borrar.

### Volver a un commit anterior {#ac8}

```sh
git reset HEAD\~4
```

El número después del `~` es la cantidad de commits hacia atrás que estamos volviendo, o en vez del `HEAD~n` puede ir el SHA identificador del commit.

Esto retrocederá el commit que elegiste, pero no eliminará los cambios hechos en los archivos, por lo que tendrás que hacer:

```sh
git checkout .
git clean -d -f
```

Si revisaste los cambios y quieres volver a tu último commit... pues no lo he descubierto aún, yo hago `git pull origin master` (si estoy en master).

Se habla mucho de `git reset --hard` pero eso no te da la posibilidad de hacer fast forward y volver a commit más recientes, simplemente elimina los commits hechos hasta ahí.

```sh
git reset --hard HEAD\~4
```

Aunque siempre puedes hacer pull desde origin.

### `Add, commit y push` en una línea {#ac10}

Resulta que hay un concatenador de comandos: `&&` , así de simple. Entonces ahora en vez de escribir:

```sh
git add -A
git commit -m "parche al reactor n.9"
git push origin master
```

... y tener que esperar que se ejecute cada comando antes de escribir el siguiente, sólo hay que escribir:

```sh
git add -A && git commit -m "parche al reactor n.9" && git push origin master
```

#### `alias` Más conveniente aún {#ac11}

Se puede guardar una combinación de comandos en un alias para que en vez de 2 comandos sea uno solo, así.

```sh
git config --global alias.add-commit '!git add -A && git commit'
```

Ahora se agrega y hacen commits con un solo comando, así:

```sh
git add-commit -m "parche al reactor n.9"
```

Y para borrar el alias si ya no gusta o uno quiere cambiarlo:

```sh
git config --global --unset alias.add-commit
```

ref:

-   <http://stackoverflow.com/questions/4298960/git-add-a-git-commit-in-one-command>

### Revisar detalle de los cambios {#ac12}

Para ver el detalle de los nuevos archivos que se van a agregar en un `add`

```sh
git status -u
```

Y si el output es muy largo uno puede usar:

```sh
git status -u | less
```

Ahí te mete en vi, para salir hay escribir: `:q` .

ref:

-   <https://www.kernel.org/pub/software/scm/git/docs/git-status.html>
-   <http://stackoverflow.com/questions/8883189/how-can-i-turn-on-a-pager-for-the-output-of-git-status>

## Conclusión

Hay un montón de trucos y comandos que te simplifican la vida en git, aquí iremos agregando a medida que los vayamos encontrando.
