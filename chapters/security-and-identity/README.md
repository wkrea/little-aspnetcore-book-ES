# Seguridad e identidad

La seguridad es una de las principales preocupaciones de cualquier aplicación web moderna o API. Es importante mantener seguros los datos de sus usuarios o usuarios y fuera del alcance de los atacantes. Este es un tema muy amplio, que involucra cosas como:

* Desinfección de entrada de datos para evitar ataques de inyección de SQL.
* Prevención de ataques de falsificación de petición en sitios cruzados(CSRF) en formularios
* Utilizando HTTPS (cifrado de conexión) para que los datos no puedan ser interceptados mientras viaja a través de Internet
* Proporcionar a los usuarios una forma segura de iniciar sesión con una contraseña u otras credenciales
* Diseño de restablecimiento de contraseñas, recuperación de cuentas y flujos de autenticación multifactor

ASP.NET Core puede ayudar a que todo esto sea más fácil de implementar. Los dos primeros (protección contra inyección de SQL y ataques de falsificación de petición en sitios cruzados) ya están incorporados, y puede agregar algunas líneas de código para habilitar el soporte de HTTPS. Este capítulo se centrará principalmente en los aspectos de identidad** de seguridad: manejo de cuentas de usuario, autenticación (inicio de sesión) de sus usuarios de forma segura y toma de decisiones de autorización una vez que se autentiquen.

> La autenticación y la autorización son ideas distintas que a menudo se confunden. **La autenticación** se ocupa de si un usuario está conectado, mientras que **la autorización** se ocupa de lo que está autorizado a hacer *después de* que inicie sesión. Se puede pensar en la autenticación como una pregunta: "¿Sé quién? este usuario es? " Mientras que la autorización pregunta: "¿Tiene este usuario permiso para hacer *X*?"

La plantilla de autenticación individual de MVC + que usó para organizar el proyecto incluye varias clases creadas sobre ASP.NET Core Identity, un sistema de autenticación e identidad que forma parte de ASP.NET Core. Fuera de la caja, esto agrega la capacidad de iniciar sesión con un correo electrónico y una contraseña.

## ¿Qué es ASP.NET Core Identity?

ASP.NET Core Identity es el sistema de identidad que se incluye con ASP.NET Core. Como todo lo demás en el ecosistema de ASP.NET Core, es un conjunto de paquetes de NuGet que se pueden instalar en cualquier proyecto (y ya están incluidos si usa la plantilla predeterminada).

ASP.NET Core Identity se encarga de almacenar las cuentas de usuario, el hash y el almacenamiento de las contraseñas y la administración de roles para los usuarios. Es compatible con el inicio de sesión de correo electrónico / contraseña, autenticación multifactorial, inicio de sesión social con proveedores como Google y Facebook, así como la conexión a otros servicios mediante protocolos como OAuth 2.0 y OpenID Connect.

Las vistas de registro e inicio de sesión que se suministran con la plantilla de MVC + autenticación individual ya aprovechan la identidad central de ASP.NET, ¡y ya funcionan! Intenta registrarte para obtener una cuenta e iniciar sesión.
