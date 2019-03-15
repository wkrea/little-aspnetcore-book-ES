# El pequeño libro de ASP.NET Core

*Por Nate Barbettini*

Copyright © 2018. Todos los derechos reservados.

ISBN: 978-1-387-75615-5

Liberado bajo la licencia Creative Commons Attribution 4.0. Eres libre de compartir, copiar y redistribuir este libro en cualquier formato o mezclarlo y transformarlo para cualquier propósito \(incluso comercialmente\). Debes de dar el crédito apropiado y proveer la liga a la licencia.

Visita [https://creativecommons.org/licenses/by/4.0/deed.es](https://creativecommons.org/licenses/by/4.0/deed.es) para obtener más información.

## Introducción
¡Gracias por elegir El pequeño libro de ASP.NET Core! Escribí este corto libro para ayudar a los desarrolladores y personas interesadas en aprender programación web a aprender acerca de ASP.NET Core 2.0, un marco de trabajo para desarrollar aplicaciones web y APIs.

El pequeño libro de ASP.NET Core está estructurado como un tutorial. Construirás una aplicación de principio a fin y con ello aprenderás:

* Los fundamentos del patrón MVC (Modelo-Vista-Controlador)
* Cómo funciona el código del lado del cliente (HTML, CSS y Javascript) junto con el código del lado del servidor.
* Qué es la inyección de dependencias y porque es tan útil
* Cómo leer y escribir datos a una base de datos
* Cómo agregar inicio de sesión, registro y seguridad
* Cómo desplegar la aplicación en la web

No te preocupes, para iniciar no necesitas conocer nada sobre ASP.NET Core (o algo de lo anterior).

## Antes de empezar

El código de la versión final de la aplicación que construirás está disponible en Github:

[https://www.github.com/nbarbettini/little-aspnetcore-todo](https://www.github.com/nbarbettini/little-aspnetcore-todo)

Siéntete libre de descargarlo si quieres ver el producto final, o compararlo mientras escribes tú propio código.

El libro mismo es actualizado frecuentemente con corrección de errores y adición de nuevo contenido. Si estas leyendo un archivo PDF, libro electrónico o versión impresa verifica el sitio web oficial ([littleasp.net/book](http://www.littleasp.net/book)) para ver si hay disponible una versión actualizada. La última página del libro contiene información sobre la versión y el registro de cambios.

## Leyendo en tu propio lenguaje

Gracias a algunos fantásticos contribuidores multilingües. El pequeño libro de ASP.NET Core ha sido traducido a otros lenguajes:

* [**ASP.NET Core El Kitabı**](https://sahinyanlik.gitbooks.io/kisa-asp-net-core-kitabi/)(Turco)

* [**简明 ASP.NET Core 手册**](https://windsting.github.io/little-aspnetcore-book/book/)(Chino)


## ¿A quién está dirigido este libro?
Si eres nuevo en la programación, esté libro te introducirá los patrones y conceptos utilizados para desarrollar aplicaciones web modernas. Aprenderás como construir una aplicación web (y cómo se ensamblan todas las piezas) ¡construyendo algo desde el inicio! A pesar de que este pequeño libro no es capaz de cubrir absolutamente todo lo que necesitas conocer acerca de la programación te dará un punto de inicio de tal forma que puedas aprender tópicos más avanzados.

Si ya haz programado en un lenguaje de lado del servidor como Node, Python, Ruby, Go o Java encontrarás muchas ideas familiares como MVC, plantillas vistas e inyección de dependencias. El código estará en C#, pero no lucirá tan diferente de lo que ya conoces. Si eres un desarollador ASP.NET MVC, ¡te sentirás como en casa! ASP.NET Core añade algunas nuevas herramientas y reutiliza (y simplifica) las cosas que ya conoces. Apuntaré sobre algunas diferencias a continuación.

No importa tú experiencia previa con la programación web, este libro te enseñará todo lo necesario para crear una aplicación web ASP.NET Core simple y útil. Aprenderás como desarrollar las funcionalidades usando el código del lado del servidor y del cliente. Cómo interactuar con una base de datos y cómo desplegar la aplicación en un servidor real.

## ¿Qué es ASP.NET Core?

ASP.NET Core es un marco de trabajo web creado por Microsoft para desarrollar aplicaciones web, APis y microservicios. Usa patrones comunes como MVC (Modelo-Vista-Controlador), inyección de dependencias y una canalización de solicitudes formada por middleware. Es open source bajo la licencia Apache 2.0, lo que significa que está disponible de forma gratuita y la comunidad es animada para contribuir con corrección de errores y adición nuevas características.

ASP.NET Core se ejecuta sobre el entorno de ejecución .NET de Microsoft, similar a la Máquina Virtual de Java (JVM) o el intérprete de Ruby. Puedes escribir aplicaciones ASP.NET Core en un número de lenguajes (C#, Visual Basic y F#). C# es la elección más popular, y es lo que usaremos en este libro. Puedes desarrollar y ejecutar aplicaciones ASP.NET Core en Windows, Mac y Linux.

## ¿Porqué necesitamos otro marco de trabajo para el desarollo web?
Al día de hoy,existen gran cantidad de marcos de trabajo web para elegir : Node/Express, Spring, Ruby on Rails, Django,Laravel y muchos más. ¿Cuáles son las ventajas que ASP.NET Core tiene?

* **Rapidez.** ASP.NET Core es rápido. Debido a que el código .NET es compilado, este se ejecuta mucho más rápido que el código de lenguajes interpretados como Javascript o Ruby. ASP.NET Core también esta optimizada para multihilos y tareas asíncronas. Es común ver una mejora en la rapidez de 5-10x sobre el código escrito en Node.js.

* **Ecosistema.** ASP.NET Core puede ser nuevo, pero .NET ha estado alrededor por un largo tiempo. Hay miles de paquetes disponibles en Nuget (el administrador de paquetes para .NET; Similar a npm, Ruby gems o Maven). Actualmente hay paquetes disponibles para deserializar JSON, conectores de base de datos, generadores de PDF o casi cualquier otra cosa que se te ocurra.

* **Seguridad.** El equipo de Microsoft se toman muy enserio la seguridad y ASP.NET Core fue creado para ser seguro desde sus fundamentos. Este maneja la limpieza de la entrada de datos y previene ataques de falsificación de ataques en sitios cruzados(CSRF), así no tienes que hacerlo tú mismo. También tienes el beneficio de tipado estático con el compilador .NET, el cuál es como tener un linter muy paranoico encendido todo el tiempo. Algunas veces esto hace más difícil hacer cosas no intencionado una variable o pedazos de datos.

## .NET Core y Estandar .NET
A lo largo de este libro, Estarás aprendiendo acerca de ASP.NET Core (el marco de trabajo web). Ocasionalmente mencionare el entorno de ejecución .NET, la librería de soporte que ejecuta el código .NET. Si esto actualmente suena como Griego para ti, ¡solo pasa al siguiente capítulo!.

Es posible que también hayas escuchado acerca del .NET Core y Estándar .NET. Estas son términos que se confunden así aquí esta una simple explicación:

**Estándar .NET** es una interfaz independiente de plataformas que define las características y APIs. Es importante recalcar que el estándar .NET no representa ninguna código o funcionalidad, solo refleja cuántas APIs están disponibles (o que tan amplia es la superficie de la API). Por ejemplo, el estándar .NET 2.0 tiene más APIs disponibles que el estándar .NET 1.5, el cuál tiene más APIs que el estándar .NET.

**.NET Core** es el motor de tiempo de ejecución de .NET que puede ser instalado en Windows, Mac o Linux. Este implementa las APIs definidas en el estándar .NET con el código especifico para cada sistema operativo. Esto es lo que instalas en tu propia máquina para programar y ejecutar aplicaciones ASP.NET Core.

Y solo como una buena medida, **.NET Framework** es una implementación diferente del estándar .NET que es específica para Windows. Este era el único entorno de ejecución .NET hasta que .NET Core llego y proporciono .NET para Mac y Linux. ASP.NET Core puede correr sobre el .NET Framework solo Windows, pero no tratare sobre esto demasiado.

Si estas confundido con todos estos nombres, ¡no te preocupes! Estaremos escribiendo algo de código real muy pronto.

## Nota para los desarrolladores de ASP.NET 4
Si no has utilizado las versiones previas de ASP.NET, puedes pasar directo al siguiente capítulo.

ASP.NET Core es una re-escritura completa desde los fundamentos de ASP.NET con el objetivo de modernizar el marco de trabajo y finalmente desacoplarlo de System.Web, ISS y Windows. Si recuerdas todo las cosas sobre OWIN/Kataba de ASP.NET 4, estas casi la mitad ahí: el proyecto Katana se convirtió en ASP.NET 5 el cual finalmente fue renombrado como ASP.NET Core.

Debido al legado de Katana, la clase `Startup` es el frente y centro, y no hay más `Application_Start` o `Global.asax`. La canalización entera esta manejada por middleware, y no hay una separación entre MVC y Web API: los controladores pueden simplemente retornar vistas, códigos de estado o datos. La inyección de dependencias viene integrada, así que no tienes que instalar y configurar un contenedor como StructureMap o Ninject si no deseas hacerlo. Y el marco de trabajo .NET ha sido optimizado para la velocidad y eficiencia del motor de tiempo de ejecución.

Bien, es suficiente de introducción. ¡Profundicemos en ASP.NET Core!