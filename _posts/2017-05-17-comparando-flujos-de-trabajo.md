---
layout: post
title: Comparando flujos de trabajo
---


La lista de posible flujos de trabajo puede hacer difícil saber por dónde empezar cuando implementamos `git` en nuestro propio trabajo. Este artículo proporciona un punto de partida echándole un vistazo a los flujos de trabajo en equipos de empresa más comunes en `git`.^[NOTA: Este artículo es una adaptación al español de la documentación oficial de GStreamer: [Comparing Workflows: Centralized|Featrue Branch|Gitflow|Forking Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows)]

A medida que leamos debemos recordar que estos flujos de trabajo están diseñados para ser más una guía que unas reglas concretas. Simplemente se quieren mostrar las cosas que es posible hacer, así que se pueden mezclar y encajar aspectos de distintos flujos de trabajo para atender a las necesidades particulares de cada cuál.

Flujo de trabajo centralizado
=============================

![](https://wac-cdn-a.atlassian.com/dam/jcr:c27e646e-0b66-49bd-9f85-ee9205e295d6/01.svg?cdnVersion=ek)

La transición a un sistema de control distribuido puede parecer una tarea una tarea abrumadora, pero en realidad *no hay motivo para cambiar de flujo de trabajo existente* para sacar ventaja de Git. Nuestro equipo de trabajo puede desarrollar proyectos de la misma manera que lo haría con Subversion.

Sin embargo, usar Git para mejorar nuestro flujo de trabajo en el desarrollo presenta algunas ventajas frente a SVN. Primero, permite que cada desarrollador tenga su propia copia *local* del proyecto entero. Este entorno aislado permite que cada desarrollador trabaje independientemente de todos los demás cambios que se hagan en el proyecto ---se pueden añadir `commits` al repositorio local y olvidarse completamente de los cambios realizados en el repositorio principal hasta que sea conveniente.

Segundo, nos da acceso al robusto modelo de ramificación y fusión de Git. A diferencia de SVN, las ramas de Git están diseñadas como un mecanismo a prueba de fallos para la integración de código y el intercambio entre repositorios.

Cómo funciona
=============

![](https://wac-cdn-a.atlassian.com/dam/jcr:24d40389-e44e-4c28-b01b-be389d52506d/02.svg?cdnVersion=em)

Igual que en Subversion, el Flujo de Trabajo Centralizado usa un repositorio central que sirve como único punto-de-entrada para todos los cambios efectuados en el proyecto. En lugar de `trunk`, la rama de desarrollo por defecto se llama `master` y todos los cambios se consignan en ella. Este flujo de trabajo no requiere más ramas a parte del `master`.

Los desarrolladores empiezan clonando el repositorio central. En sus propias copias locales del proyecto, editan los archivos deseados y se consignan con un `commit`, como se haría en SVN; sin embargo, esos nuevos `commit` se almacenan *localmente* ---permanecerán completamente aislados del repositorio central. Esto permite a los desarrolladores diferir la sincronía con el flujo principal hasta que se encuentren en un punto de giro.

Para publicar los cambios en el proyecto oficial, los desarrolladores realizan un `push` desde la rama `master` de su copia local al repositorio central. Este es el equivalente al `commit` de SVN, salvo que en este caso se añaden todos los `commit` locales que no estan todavía en la rama `master` central.

![Repositorio Central](https://wac-cdn-a.atlassian.com/dam/jcr:24d40389-e44e-4c28-b01b-be389d52506d/02.svg?cdnVersion=em)

![Repositorio Local](https://wac-cdn-a.atlassian.com/dam/jcr:24d40389-e44e-4c28-b01b-be389d52506d/02.svg?cdnVersion=em)

Manejando conflictos
--------------------

El repositorio central representa el proyecto oficial, por lo que su historia de commits debería ser sagrada e inmutable. Si el historial de `commit` locales diverge del repositorio central, Git rechazará los cambios, puesto que sobreescribiría los `commit` oficiales.

![Repositorio Local](https://wac-cdn-a.atlassian.com/dam/jcr:466a59b3-9175-47f7-85d5-48c926ed9555/03.svg?cdnVersion=em)

Antes de que el desarrollador pueda publicar su aportación, deberá ir a buscar (`fetch`) los `commit` actualizados del repositorio central y basar los cambios de su aportación en ellos (`rebase`). Es como decir: "quiero añadir mis cambios a lo que se ha hecho hasta ahora". El resultado de esto es un historial perfectamente lineal, precisamente como son los flujos de trabajo tradicionales de SVN.

Si los cambios locales entran directamente en conflicto con el historial de `commit` de la versión principal, Git pausará el proceso de `rebase`, dándonos la oportunidad de resolver esos confilictos a mano. Lo bueno de Git es que usa los mismos comandos `git status` y `git add` tanto para generar un `commit` como para solucionar conflictos en la fusión de dos ramas. Esto facilita el manejo de sus propias fusiones a los nuevos desarrolladores. Además, si se meten en líos, Git hace muy fácil abortar el proceso de `rebase` entero y volver a intentarlo (o buscar ayuda).

Ejemplo
-------

Veamos ahora paso a paso cómo colaboraría un equipo pequeño empleando este flujo de trabajo. Veremos cómo dos desarrolladores, Juan y María, pueden trabajar en partes distintas de un proyecto y compartir sus contribuciones en un repositorio central.

1. Alguien inicia el repositorio central: ![](https://wac-cdn-a.atlassian.com/dam/jcr:4495bbe0-c932-4f82-afa2-3cdf275fe1f8/04.svg?cdnVersion=en) Primero alguien tiene que crear el repositorio central en un servidor. Si es un proyecto nuevo, podemos iniciar un repositorio vacío. De otro modo, tendremos que importar un repositorio Git o SVN.

Los repositorios centrales deberían ser siempre repositorios `bare` (no deberían tener un *directorio de trabajo*), que se pueden crear como sigue:

```bash
ssh usuario@servidor git init --bare /ruta/al/repositorio.git
```

Nos aseguraremos de dar un nombre válido de `usuario` a SSH, el dominio o dirección ip de nuestro `servidor`, y la localización donde tenemos el repositorio en la `ruta/al/repositorio`. La extensión `.git` se añade por convención al nombre del repositorio para indicar que es un repositorio desnudo (`bare`).

2. Todo el mundo clona el repositorio central: ![](https://wac-cdn-a.atlassian.com/dam/jcr:59fe7131-1ffa-4a58-bcbe-eaecb1bf3ebb/05.svg?cdnVersion=en) A continuación, cada desarrollador crea una copia local del proyecto entero. Esto se consigue con el comando `git clone`:

```bash
git clone ssh://usuario@servidor/ruta/al/repositorio.git
```

Cuando clonamos un repositorio, Git automáticamente añade un acceso directo bajo el nombre de `origin` reenvía información al repositorio "padre", bajo la asunción de que querremos interactuar con él tarde o temprano.

3. Juan trabaja en su contribución: ![](https://wac-cdn-a.atlassian.com/dam/jcr:df3bad19-f053-4a79-8825-d0916c140d10/06.svg?cdnVersion=en) En su repositorio local, Juan puede desarrollar sus contribuciones usando los comandos ordinarios de los procesos de consigna de Git: `edit`, `stage` y `commit`. Para quien no esté familiarizado con el area de preparación (*staging area*), se trata de un modo de preparar una consigna sin tener que incluir todos los cambios en el directorio de trabajo. Esto nos permite crear consignas extremadamente concisas, incluso cuando hemos realizado muchos cambios por el camino.

```bash
git status		# Ver el estado del repositorio
git add <archivo*>	# Colocar en el area de preparación 
    			# (stage) el archivo
git commit		# Consignar (commit) los archivos en
    			# el area de preparación
```

Recordemos que dado que estos comandos crean consignas locales, Juan puede repetir el proceso tantas veces como quiera sin preocuparse de lo que ocurra en el repositorio central. Esto puede resultar sumamente útil para cambios sustanciales que necesitan dividirse en otros más simples, fragmentos más atómicos.

4. María trabaja en su contribución: ![](https://wac-cdn-a.atlassian.com/dam/jcr:a0aa8d1b-11a4-4aa4-a3a9-4f83f9be9a67/07.svg?cdnVersion=en) Mientras tanto, María está trabajando en sus propias contribuciones en su repositorio local usando el mismo proceso de edición/preparación/consigna. Al igual que Juan, no tiene que preocuparse por lo que ocurra en el repositorio central, y realmente *no le importa* lo que esté haciendo Juan en su repositorio local, dado que todos los repositorios privados son *privados*.

5. Juan publica sus cambios: ![](https://wac-cdn-a.atlassian.com/dam/jcr:6e5dc66d-b041-4013-b321-b1908fecfdbd/08.svg?cdnVersion=en) Una vez Juan termina sus mejoras, debería publicar sus `commit` locales en el repositorio central, de modo que los miembros del equipo tengan acceso a ellos. Puede hacerlo con el comando `git push`, como se muestra a continuación:

```bash
git push origin master
```

Recordemos que ese `origin` no es otra cosa que la conexión remota al repositorio central que Git creó cuando Juan lo clonó. El `master` es el argumento que le dice a Git que debe intentar que la rama `master` de `origin` adopte la forma de la rama `master` local. Dado que el repositorio central no se ha actualizado desde que Juan lo clonó, esto no supondrá conflicto alguno, y el `push` funcionará según lo esperado.

6. María trata de publicar sus cambios: ![](https://wac-cdn-a.atlassian.com/dam/jcr:52e2347e-b8e0-49ab-9530-5d1e9129198e/09.svg?cdnVersion=en) Veamos ahora qué pasa si María intenta subir sus cambios al repositorio central después de que Juan haya publicado exitosamente los suyos. Puede usar exactamente el mismo comando:

```bash
git push origin master
```

Pero, dado que su historial local diverge del repositorio central, Git rechazará la petición con un mensaje de error bastante verborreico:

```bash
error: failed to push some refs to 'ruta/al/repositorio.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
hint: before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Esto evita que María sobreescriba algún `commit` oficial. Necesita bajarse (`pull`) las actualizaciones de Juan a su repositorio, integrarlas con sus cambios locales, e intentarlo de nuevo.

7. María rebasa sus cambios sobre los `commit`(s) de Juan: ![](https://wac-cdn-a.atlassian.com/dam/jcr:25edd772-a30a-475a-a6ca-d1055ae61737/10.svg?cdnVersion=en) María puede usar `git pull` para incorporar los cambios del repositorio central a su repositorio local. Este comando es parecido al comando `svn update` ---descarga todo el `commit` principal en el repositorio de María y trata de intagrarlo con sus `commit` locales:

```bash
git pull --rebase origin master
```

La opción `--rebase` le dice a Git que mueva todos los commits de María al final de la rama `master` después de sincronizarlos con los cambios del repositorio central, como se muestra a continuación:

![](https://wac-cdn-a.atlassian.com/dam/jcr:5165668f-b62d-4417-95e6-fde8ed97ec60/11.svg?cdnVersion=en)

El `pull` aún funcionará si olvidamos esta opción, pero entonces incordiaremos con un `merge commit` superfluo cada vez que alguien necesite sincronizar con el repositorio central. Para este flujo de trabajo, es siempre mejor `rebase` en lugar de generar un `merge commit`.

8. María resuelve sus conflictos en el `merge`: ![](https://wac-cdn-a.atlassian.com/dam/jcr:eaad29a3-6d94-4916-8a2c-3dea71aea4c2/12.svg?cdnVersion=en) Rebasar trabajos transfiriendo cada `commit` local a la rama `master` actualizada, uno a uno. Esto significa que tomamos los conflictos de la fusión con `merge` con un enfoque `commit` a `commit`, en lugar de tratar de solventarlo todo con un `merge commit` masivo. Esto mantiene nuestros `commit` lo más focalizados que sea posible, y permite un historial limpio de versiones del proyecto. Uno a uno, es más fácil figurarse dónde se han introducido *bugs* y, si es preciso, deshacer los cambios con el impacto mínimo sobre el resto del proyecto.

Si María y Juan trabajan en cambios no relacionados, es improbable que el proceso de `rebase` genere conflictos. Pero si lo hace, Git detendrá el proceso en el `commit` actual y devolverá el siguiente mensaje, junto con algunas instrucciones relevantes:

```bash
CONFLICT (content): Merge conflict in <archivo>
```

![](https://wac-cdn-a.atlassian.com/dam/jcr:adf8c8e3-4287-4ec1-acf7-2a052d61d03f/13.svg?cdnVersion=en)

Lo bueno de Git es que *cualquiera* puede resolver sus propios conflictos al combinar repositorios. En nuestro ejemplo, María simplemente ejecutaría `git status` para ver dónde está el problema. Los archivos en conflicto aparecerán en la sección de `Unmerged paths:`

```bash
# Unmerged paths:
# (use "git reset HEAD <archivo>..." to unstage)
# (use "git add/rm <archivo>..." as appropriate to mark resolution)
#
# both modified: <archivo>
```
Entonces, editará los archivos a su gusto. Una vez esté conforme con el resultado, puede colocar los filtros en la zona de pereparación (*stage*) como hace habitualmente y dejar que `git rebase` haga el resto.

```bash
git add <archivo>
git rebase --continue
```
Y eso es todo lo que hay que hacer. Git se moverá al siguiente `commit` y repetirá el proceso para todos los otros `commit` que generen conflictos.

Si llegados a este punto nos damos cuenta de que no tenemos ni idea de lo que estamos haciendo, que no cunda el pánico. Sólo tenemos que ejecutar el siguiente comando y volveremos al punto de partida antes de que ejecutáramos `git pull --rebase`:

```bash
git rebase --abort
```
9. María publica sus cambios con éxito: ![](https://wac-cdn-a.atlassian.com/dam/jcr:de2dabdd-542f-4f64-9be4-870abff06f60/14.svg?cdnVersion=en) Después de haber sincronizado su repositorio con el central, María podrá publicar sus cambios exitosamente:

```bash
git push origin master
```

