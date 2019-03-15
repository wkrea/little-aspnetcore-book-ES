# Crear un controlador

Actualmente ya hay algunos controladores en la carpeta Controllers, incluyendo `HomeController` que generá la pantalla de bienvenida por defautl cuando visitas `http://localhost:5000`. Puedes ignorar estos controladores por ahora.

Crea un nuevo controlador para la funcionalidad de la lista de tareas y agrega el siguiente código:

**Controllers/TodoController.cs**

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;

namespace AspNetCoreTodo.Controllers
{
    public class TodoController : Controller
    {
        // Las acciones van aquí
    }
}
```

Las rutas que son manejadas por el controlador son llamadas **acciones**, y son representadas por métodos en la clase controlador. Por ejemplo, el `HomeController` incluye tres métodos de acción (`Index`, `About`, y `Contact`) las cuales son mapeadas por ASP.NET Core a estas rutas URLs:

```
localhost:5000/Home         -> Index()
localhost:5000/Home/About   -> About()
localhost:5000/Home/Contact -> Contact()
```

Hay un gran número de convenciones (patrones comunes) usados por ASP.NET Core, tales como patrón que `FooController` se convierte en `/Foo`, y la acción `Index` puede ser omitida de la URL. Puedes personalizar este comportamiento si así lo deseas, pero por ahora, usaremos las convenciones predefinidas.

Agrega un nueva acción llamada `Index` al `TodoController`, remplazando el comentario `// Las acciones van aquí`;

```csharp
public class TodoController : Controller
{
    public IActionResult Index()
    {
        // Obtener las tareas desde la base de datos

        // Colocar los tareas en un modelo

        // Genera la vista usando el modelo
    }
}
```

Los métodos de acción pueden regresar vistas, datos JSON, o códigos de estatus HTTP como `200 OK` y `404 Not Found`.

El tipo de retorno `IActionResult` te da la flexibilidad de regresar cualquiera de estos desde una acción. Es una buena práctica mantener los controladores tan ligeros como sea posible. En este caso, el controlador será responsable de obtener la lista de tareas desde la base de datos, poniendo estas tareas en un modelo que la vista pueda entender, y enviara la vista de regreso al navegador del usuario.

Antes de que puedas escribir el resto del código del controlador, necesitas crear un modelo y una vista.
