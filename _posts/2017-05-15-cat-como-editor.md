---
layout: post
title: cat como editor de textos
---

Si valoramos los programas no por lo que nos permiten hacer, sino por lo que nos obligan a pensar, concat es nuestro editor de texto.

`cat` es la invocación del programa concat desde la línea de comandos. Lo que hace `cat` es, simplemente, concatenar la información que entra y ofrecerla como salida. Si la entrada es un conjunto de archivos, juntará su contenido, si es un conjunto de caracteres, encadenará dichos caracteres y, por tanto, editará el texto que compongan. Veamos a modo de ejemplo qué ocurre si introducimos lo siguiente en nuestra línea de comandos:

```bash
cat<<FIN>archivo.txt

Este comando sirve para escribir un archivo de texto al que llamaremos \`archivo.txt\`

FIN
```


Con el comando anterior, lo que hacemos es usar concat como editor de un texto que dice: "Este comando sirve para escribir un archivo de texto al que llamaremos `archivo.txt`"

Con `cat` concatenamos  la entrada que escribamos tras  `intro`. Con `<<` advertimos de que queremos que concat finalice una vez hayamos escrito `FIN` ocupando una línea entera, y con `>` desviamos la salida de datos a `archivo.txt` en lugar de a la pantalla.