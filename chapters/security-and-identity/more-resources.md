## Más recursos

ASP.NET Core Identity le ayuda a agregar las características de seguridad e identidad como inicio de sesión y registro a su aplicación. Las plantillas `dotnet new` le brindan vistas y controladores predefinidos que manejan estos escenarios comunes para que pueda comenzar a trabajar rápidamente.

Hay mucho más que puede hacer ASP.NET Core Identity, como restablecer la contraseña y el inicio de sesión social. La documentación disponible en http://docs.asp.net es un recurso fantástico para aprender a agregar estas funciones.

### Alternativas a la identidad central de ASP.NET

ASP.NET Core Identity no es la única forma de agregar funcionalidad de identidad. Otro enfoque es utilizar un servicio de identidad alojado en la nube como Azure Active Directory B2C u Okta para manejar la identidad de su aplicación. Puedes pensar en estas opciones como parte de una progresión:

* **Seguridad haz la tu mismo**: no se recomienda, a menos que sea un experto en seguridad.
* **ASP.NET Core Identity**: Con las plantillas se obtiene una gran cantidad de código de forma inmediata, lo que hace que sea bastante fácil comenzar. Aún así deberá escribir algo de código para escenarios más avanzados y mantener una base de datos para almacenar información del usuario.
* **Servicios de identidad alojados en la nube**. El servicio maneja escenarios simples y avanzados (autenticación multifactor, recuperación de cuenta, federación) y reduce significativamente la cantidad de código que necesita escribir y mantener en su aplicación. Además, los datos confidenciales del usuario no se almacenan en su propia base de datos.

Para este proyecto, ASP.NET Core Identity es una excelente opción. Para proyectos más complejos, recomiendo investigar un poco y experimentar con ambas opciones para comprender cuál es la mejor para su caso de uso.