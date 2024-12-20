---
title: Cómo actualizar Drupal Core usando linea de comando\
summary: "Lista de comandos para hacer copy paste y una pequeña explicación de cómo funciona el proceso."
classes: language-php\
date: 2013-04-18T20:40:04
tags: ["php", "drupal", "short"]
---

# Actualizar Drupal usando SSH

## Lista de comandos para hacer copy paste y una pequeña explicación

Muchas veces es una lata descargar un archivo, descomprimirlo, subirlo por FTP, arrastrar archivos y carpetas de un lado para otro para actualizar el core de Drupal 7. Y como dicen que un buen programador es flojo, pues aquí pego la secuencia de comandos que uso para respaldar y actualizar el core de Drupal 7, usando SSH.

## _<span lang="fr">Mise en place</span>_ {#ac2}

Lo que debes tener es:

- Acceso SSH a tu servidor.
- Línea de comando para acceder, yo uso PuTTY (en Mac y Linux se usa la línea de comando).
- Tener los datos de la base de datos (servidor, nombre, usuario, contraseña)

## Los comandos {#ac3}

```sh
cd domains/drupalsite.com/html\
mkdir respaldos\
mkdir respaldos/2013-04-18\
mysqldump --add-drop-table -h internal-db.s00000.gridserver.com -u db00000_username -p db00000_databasename &gt; respaldos/2013-04-18/respaldo_drupal.sql\
// Aquí va a pedir la clave de la base de datos\
zip -r respaldos/2013-04-18/respaldo_drupal.zip html\
wget http://ftp.drupal.org/files/projects/drupal-7.22.zip\
// Este archivo se saca de http://drupal.org/project/drupal (ojo que sea el zip y no el tar)\
unzip drupal-\*\
mv drupal-\* html_new\
rm -r -f html_new/sites\
cp -r html/sites html_new/sites\
cp html/.htaccess html_new/.htaccess\
cp html/robots.txt html_new/robots.txt\
mv html html_old\
mv html_new html\
chmod -R 755 html_old\
rm -rf html_old\
rm drupal-\*
```

En este caso muestro la secuencia para respaldar y actualizar en un servidor (gs) de mediatemple. Para hacerlo en otro servidor hay que hacer los ajustes de direcciones.

## Referencia {#ac5}

- [Upgrading Drupal rapidly using SSH/Shell commands](http://drupal.org/node/297496)
- [Export and Import MySQL Databases on Mediatemple](https://kb.mediatemple.net/questions/129/Export+and+import+MySQL+databases#gs/method2-export)
