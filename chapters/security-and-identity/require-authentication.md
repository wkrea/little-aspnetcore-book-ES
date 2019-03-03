## Requerir autenticación

A menudo, deseará que el usuario inicie sesión antes de poder acceder a ciertas partes de su aplicación. Por ejemplo, tiene sentido mostrar la página de inicio a todos (ya sea que haya iniciado sesión o no), pero solo mostrar su lista de tareas después de haber iniciado sesión.

Puede usar el atributo `[Authorize]` en ASP.NET Core para requerir que un usuario que haya iniciado sesión para una acción particular, o un controlador completo. Para requerir autenticación para todas las acciones del `TodoController`, agregue el atributo encima de la primera línea del controlador:

**Controllers/TodoController.cs**

```csharp
[Authorize]
public class TodoController : Controller
{
    // ...
}
```
Agregue esta declaración `using` en la parte superior del archivo:

```csharp
using Microsoft.AspNetCore.Authorization;
```

Intenta ejecutar la aplicación y acceder a `/todo` sin iniciar sesión. Serás redirigido a la página de inicio de sesión automáticamente.

> El atributo `[Authorize]` en realidad está haciendo una verificación de autenticación aquí, no una verificación de autorización (a pesar del nombre del atributo). Más adelante, utilizará el atributo para verificar **tanto** la autenticación como la autorización.