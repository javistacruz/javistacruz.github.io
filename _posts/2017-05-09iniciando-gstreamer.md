---
layout: post
title: Iniciando GStreamer
---

> ***NOTA:* Lo que sigue es una adaptación al español de la documentación oficial de GStreamer: [Initializing GStreamer](https://gstreamer.freedesktop.org/documentation/application-development/basics/init.html)**

Cuando escribimos una aplicación con GStreamer, tendremos acceso a su bibliotecas de funciones simplemente incluyendo `gst/gst.h` en la cabecera. Además de eso, también necesitaremos iniciar la biblioteca GStreamer.

Iniciación simple
=================

Antes de que podamos usar las bibliotecas de GStreamer, tendremos que llamar a la función `gst_init` desde la aplicación principal. Dicha llamada realizará todo lo necesario para la iniciación, así como procesar opciones de línea de comandos específicas de GStreamer.

Un programa típico^[El código para este ejemplo se ha extraído automáticamente de la documentación y se ha articulado bajo `tests/examples/manual` en el tarball de GStreamer.] tendría un código de iniciación de GStreamer parecido a éste:

```c
#include <stdio.h>
#include <gst/gst.h>

int
main (int   argc,
      char *argv[])
{
  const gchar *nano_str;
  guint major, minor, micro, nano;

  gst_init (&argc, &argv);

  gst_version (&major, &minor, &micro, &nano);

  if (nano == 1)
    nano_str = "(CVS)";
  else if (nano == 2)
    nano_str = "(Prerelease)";
  else
    nano_str = "";

  printf ("This program is linked against GStreamer %d.%d.%d %s\n",
          major, minor, micro, nano_str);

  return 0;
}

```
Usamos las macros `GST_VERSION_MAJOR`, `GST_VERSION_MINOR` y `GST_VERSION_MICRO` para obtener la versión de GStreamer que hemos articulado, o usamos `gst_version` para obtener la versión a la que está enlazada nuestra aplicación. GStreamer actualmente usa un esquema donde las versiones con la misma *major* y *minor version* son API-/ y ABI-compatibles.

También se puede llamar a la función `gst-init` con dos argumentos nulos (`NULL`), en cuyo caso GStreamer no procesará ninguna opción de linea de comandos.

La interfaz GOption
===================

También podemos usar una tabla GOption para iniciar nuestros propios parámetros como se muestra en el ejemplo siguiente:

```c

#include <gst/gst.h>

int
main (int   argc,
      char *argv[])
{
  gboolean silent = FALSE;
  gchar *savefile = NULL;
  GOptionContext *ctx;
  GError *err = NULL;
  GOptionEntry entries[] = {
    { "silent", 's', 0, G_OPTION_ARG_NONE, &silent,
      "do not output status information", NULL },
    { "output", 'o', 0, G_OPTION_ARG_STRING, &savefile,
      "save xml representation of pipeline to FILE and exit", "FILE" },
    { NULL }
  };

  ctx = g_option_context_new ("- Your application");
  g_option_context_add_main_entries (ctx, entries, NULL);
  g_option_context_add_group (ctx, gst_init_get_option_group ());
  if (!g_option_context_parse (ctx, &argc, &argv, &err)) {
    g_print ("Failed to initialize: %s\n", err->message);
    g_clear_error (&err);
    g_option_context_free (ctx);
    return 1;
  }
  g_option_context_free (ctx);

  printf ("Run me with --help to see the Application options appended.\n");

  return 0;
}



```

Como se muestra en este fragmento, podemos usar una tabla [GOption](http://developer.gnome.org/glib/stable/glib-Commandline-option-parser.html) para definir las opciones de línea de comandos específicas de nuestra aplicación, y pasar esa tabla al inicio de la función GLib junto con el gropo de opciones devuelto por la fución `gst_init_get_option_group`. Las opciones de nuestra aplicación se procesarán junto a las opciones genéricas de Gstreamer.