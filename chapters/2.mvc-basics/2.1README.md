# Fundamentos de MVC
En este capítulo, explorarás el patrón MVC en ASP.NET Core. **MVC** (Modelo-Vista-Controlador) es un patrón para crear aplicaciones web que es usado en casi todos los marcos de desarollo web (ejemplos populares son Ruby on Rails y Express), adicionalmente marcos de trabajo del lado de cliente con Javascript como Angular. Las aplicaciones móviles sobre iOS y Android también usan una variante de MVC.

Como el nombre sugiere MVC tiene tres componentes: modelos, vistas y controladores. Los **Controladores** gestionan las solicitudes de entrada desde un cliente o un navegador web y deciden acerca de que código ejecutar. Las **vistas** son plantillas (usualmente HTML más un lenguaje de plantillas como Handlebars, Pug o Razor) que contienen datos añadidos a el que luego son mostrados a los usuario.Los **Modelos** mantienen los datos que son agregado a las vistas, o los datos que son ingresados por los usuarios.

Un patrón común para el código MVC es:

* El controlador recibe una petición y busca alguna información en una base de datos.
* El controlador crea un modelo con la información y la adjunta a la vista.
* La vista es generada y mostrada en el navegador del usuario.
* El usuario presiona un botón o envía un formulario, lo que enviá una nueva solicitud al controlador y el ciclo se repite.

Si has trabajado con MVC en otros lenguajes, te sentirás como en casa en ASP.NET Core MVC. Si eres nuevo en MVC, este capítulo te enseñara lo básico y te ayudará a iniciar.

## Lo que vas a programar
El ejercicio de "Hola Mundo" de MVC es construir una aplicación de lista de tareas. Es un proyecto genial ya que es pequeño y simple en alcance, pero trata cada una de las partes de MVC y cubre muchos conceptos que usaras en un aplicación más grande.

En este libro, desarrollaras una aplicación de gestión de tareas pendientes que dejara al usuario agregar elementos a su lista de tareas y una vez que la tarea se ha completado. Más específicamente estarás creando:

* Una aplicación web de servidor (a veces llamada Back-End) usando a ASP.NET Core, C# y el patrón MVC.
* Una base de datos para almacenar la lista de tareas del usuario usando el motor de base de datos SQLite y un sistema llamado Entity Framework Core.
* Las páginas web y la interfaz con la que el usuario interacturá vía el navegador. Usando HTML, CSS y Javascript (llamado el FrontEnd).
* Un formulario de inicio de sesión y verificación de seguridad así cada usuario mantendrá su lista de tareas privada.

 ¿Suena bien? ¡Vamos a construirla! si no haz creado una aplicación nueva en ASP.NET Core proyecto usando `dotnet new mvc`, sigue los pasos en el capítulo anterior, debes ser capaz de construir y ejecutar el proyecto y ver la pantalla de bienvenida,