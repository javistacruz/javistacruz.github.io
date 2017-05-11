---
layout: post
title: Git de supervivencia
---

A continuación se emprende una exposición esquemática de los comandos imprescindible para sobrevivir usando `git`. 

Comandos básicos
----------------

Aún aquellos que no sean usuarios de `git` deberían conocer el comando:


```bash
git clone <url-repositorio>
```

que nos permite descargar un repositorio a nuestro disco duro para hacer lo que queramos con él.

Si queremos hacer algo más que descargar (algo que se puede conseguir también con `curl` o `wget`), entonces tendremos que usar los comandos `status` y `commit`, sin olvidar `init`, cuando queramos empezar un repositorio desde cero por nuestra cuenta. 

Si queremos saber cuál es el estado de nuestro repositorio, usaremos:


```bash
git status
```

que nos permite saber qué hemos cambiado desde la última versión (en principio, la que hemos clonado del repositorio). Si quisiéramos subir los cambios al repositorio, o simplemente establecer una nueva versión que incluya nuestros cambios, usaremos el comando estrella de git: `commit` (consignar), que establece un *punto de control* al que podremos volver en un futuro, es decir, lo que ramplonamente se llama *deshacer los cambios* que hayamos hecho después del `commit`. Al comando se le suele añadir el sufijo -m y a continuación una breve descripción de los cambios consignados, como en el ejemplo siguiente:

```bash
git commit -m "corregida puntuación"
```

Ahora bien, `comit` no consigna *todos* los cambios realizados en el directorio, sino sólo los de aquellos archivos de los que esté haciendo un seguimiento. Veremos a continuación qué significa eso. 


Creando nuestros propios repositorios
-------------------------------------

Si lo que queremos es, no ya trabajar sobre repositorios ajenos, sino llevar el control de nuestros propios archivos, querremos crear nuestro propio repositorio. Lo único que tendremos que hacer es ir al directorio del que queremos hacer un seguimiento y ejecutar en nuestra terminal el comando:

```bash
git init
```

Esto creará un directorio oculto `.git` donde se irá almacenando toda la información necesaria para conservar el historial de nuestro repositorio. Si borramos `.git` del repositorio, será como si nunca hubiéramos iniciado git en él. Lo que ocurrirá será que nos quedaremos con los archivos tal cual están, pero sin poder acceder a sus estados previos.

Veámoslo en forma de ejemplo: supongamos que creamos el archivo `/tmp/proyecto/borrador.md`, en el que

```bash
cat<<FIN>/tmp/proyecto/borrador.md

Este documento pretende funcionar como ejemplo de archivo cambiante para ver cómo pueden crecer y ramificarse los archivos con \`git\`.

FIN

```

Y a continuación exportamos con `pandoc borrador.md -o borrador.pdf` para ver cómo nos ha quedado la obra maestra. Naturalmente, nos interesa hacer un seguimiento de los brutos `.md` sobre los que trabajamos, no sus exportaciones en `.pdf`, que no podemos modificar directamente. Así que `git` no hará un seguimiento de todos los archivos del directorio, sino sólo de aquellos que le solicitemos con `add`. En el caso de nuestro ejemplo:

```bash
git add borrador.md
```

Si ahora hacemos un `git status` obtendremos:^[Como se puede observar, el propio `git` nos va refrescando en todo momento la memoria acerca de lo que podemos hacer, de modo que siempre será útil leer los mensajes que nos devuelve la consola, sobre todo si queremos usar un comando que tenemos en la punta de la lengua pero no acaba de salir.]

```bash
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   borrador.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	borrador.pdf

```

Donde vemos que `borrador.pdf` aparece como archivo sin seguimiento y, por el contrario, `borrador.md` está listo para un `commit`. De modo que estableceremos un punto de inicio con:

```bash
git commit -m "Inicial"
```

Viajando a través del tiempo
----------------------------

Hasta aquí los prolegómenos. Ahora empieza la función.

Nuestro borrador sufre diversas modificaciones, que iremos consignando en sus respectivos `commit`s: "Inicial", "corrección puntuación", "añadida sección 2", "enlace entre secciones", "sección 1 mejorada", "corrección de puntuación", "versión final". Pero cuando creíamos haber acabado, nos queda la duda de si realmente deberíamos haber "mejorado" la sección 1, o si mejor haberla dejado como está, y como hemos llevado un control de versiones, decidimos dar a un par de amigos la "versión final", y a otro par de amigos la versión con la "sección 1 mejorada".

Vemos cómo hacer esto:

```bash
pasito-palante-patrás
```

Esto ya es algo que, a menos que hayamos ido guardando archivos versionados a mano, no podremos hacer con un simple editor de texto. Y si hemos ido guardando distintos archivos versionados a mano, entonces nosotros somos el controlador de versiones. No solo `git`, sino también CVS, Subversion, SourceSafe, ClearCase, Darcs, Bazaar, Plastic SCM, Mercurial, Perforce, Fossil SCM o Team Foundation Server son sistemas automatizados de versiones, cada uno con sus puntos fuertes y débiles. De ellos, quizá sea `git` el más versátil y robusto, y su punto fuerte es su capacidad para gestionar versiones distribuídas, o lo que es lo mismo, versionar documentos en los que participan muchas personas, cada una con su copia y sus retoques en su propio disco duro.

Por ponerlo en forma de ejemplo, de las copias que hemos distribuído, cada uno se forma una opinión, y también cada uno encuentra distintos errores que corrige en la copia supervisada que nos devuelve. Donde `git` es mejor que otros sistemas es en recoger todas estas correcciones, recordando no solo la versión sobre la que se aplican, sino también recordando quién las hizo, para poder aprovechar al máximo toda esta información en la versión final sin que nosotros tengamos que corregir ni una sola letra.

Sean Ana y Ángel los correctores de la "sección 1 mejorada"



De la acumulación a la ramificación
-----------------------------------

Con lo visto hasta aquí se pueden ir acumulando materiales y versionándolos, igual que el geólogo puede ir datando las distintas capas de suelo. Pero se trata de estratos ya inoperativos, piel muerta, sobre los que prevalece triunfante la última versión. En la práctica, esta forma de versionar no tiene otro sentido que la mera documentación arqueológica.

Si queremos insuflar vida a nuestro flujo de trabajo debemos dejar de pensar términos geológicos y encontrar una metáfora más cercana a la biología. Y esta metáfora la encontramos en la ramificación que produce el comando `branch` (rama). Con él, nuestros proyectos empiezan a comportarse como organismos vivos, por ejemplo, como un árbol.