## Usando la identidad en la aplicación

Los elementos de la lista de tareas pendientes todavía se comparten entre todos los usuarios, porque las entidades de tareas pendientes almacenadas no están vinculadas a un usuario en particular. Ahora que el atributo `[Authorize]` asegura que debe iniciar sesión para ver la vista de tareas, puede filtrar la consulta de la base de datos según quién haya iniciado sesión.

Primero, inyecte un `UserManager<ApplicationUser>` en el `TodoController`:

**Controllers/TodoController.cs**

```csharp
[Authorize]
public class TodoController : Controller
{
    private readonly ITodoItemService _todoItemService;
    private readonly UserManager<ApplicationUser> _userManager;

    public TodoController(ITodoItemService todoItemService,
        UserManager<ApplicationUser> userManager)
    {
        _todoItemService = todoItemService;
        _userManager = userManager;
    }

    // ...
}
```

Deberá agregar una nueva declaración `using` en la parte superior:

```csharp
using Microsoft.AspNetCore.Identity;
```

La clase `UserManager` es parte de ASP.NET Core Identity. Puedes usarlo para obtener al usuario actual en la acción `Index`:

```csharp
public async Task<IActionResult> Index()
{
    var currentUser = await _userManager.GetUserAsync(User);
    if (currentUser == null) return Challenge();

    var items = await _todoItemService
        .GetIncompleteItemsAsync(currentUser);

    var model = new TodoViewModel()
    {
        Items = items
    };

    return View(model);
}
```

El nuevo código en la parte superior del método de acción utiliza el `UserManager` para buscar al usuario actual en la propiedad` Usuario` disponible en la acción:

```csharp
var currentUser = await _userManager.GetUserAsync(User);
```

Si hay un usuario que ha iniciado sesión, la propiedad `User` contiene un objeto ligero con algo (pero no toda) la información del usuario. El `UserManager` usa esto para buscar los detalles completos del usuario en la base de datos a través del método `GetUserAsync() `.

El valor de `currentUser` nunca debe ser nulo, porque el atributo `[Authorize]` está presente en el controlador. Sin embargo, es una buena idea hacer un control de cordura, por si acaso. Puede usar el método `Challenge()` para forzar al usuario a iniciar sesión nuevamente si falta su información:

```csharp
if (currentUser == null) return Challenge();
```

Como ahora estás pasando un parámetro `ApplicationUser` a `GetIncompleteItemsAsync()`, deberás actualizar la interfaz `ITodoItemService`:

**Services/ITodoItemService.cs**

```csharp
public interface ITodoItemService
{
    Task<TodoItem[]> GetIncompleteItemsAsync(
        ApplicationUser user);
    
    // ...
}
```

Ya que cambió la interfaz `ITodoItemService`, también necesita actualizar la firma del método `GetIncompleteItemsAsync()` en el `TodoItemService`:

**Services/TodoItemService**

```csharp
public async Task<TodoItem[]> GetIncompleteItemsAsync(
    ApplicationUser user)
```

El siguiente paso es actualizar la consulta de la base de datos y agregar un filtro para mostrar solo los elementos creados por el usuario actual. Antes de que pueda hacer eso, debe agregar una nueva propiedad a la base de datos.

### Actualizar la base de datos

Deberá agregar una nueva propiedad al modelo de entidad `TodoItem` para que cada elemento pueda "recordar" al usuario que lo posee:

**Models/TodoItem.cs**

```csharp
public string UserId { get; set; }
```

Dado que ha actualizado el modelo de entidad utilizado por el contexto de la base de datos, también debe migrar la base de datos. Crea una nueva migración usando `dotnet ef` en el terminal:

```
dotnet ef migrations add AddItemUserId
```

Esto crea una nueva migración llamada `AddItemUserId` que agregará una nueva columna a la tabla `Items`, reflejando el cambio realizado en el modelo `TodoItem`.

Utilice `dotnet ef` de nuevo para aplicarlo a la base de datos:

```
dotnet ef database update
```

### Actualizar la clase de servicio

Con la base de datos y el contexto de la base de datos actualizados, ahora puede actualizar el método `GetIncompleteItemsAsync()` en el `TodoItemService` y agregar otra cláusula a la declaración `Where`:

**Services/TodoItemService.cs**

```csharp
public async Task<TodoItem[]> GetIncompleteItemsAsync(
    ApplicationUser user)
{
    return await _context.Items
        .Where(x => x.IsDone == false && x.UserId == user.Id)
        .ToArrayAsync();
}
```

Si ejecuta la aplicación y se registra o inicia sesión, verá una lista de tareas vacía una vez más. Desafortunadamente, cualquier tarea que intentes agregar desaparece en el éter, porque aún no has actualizado la acción `AddItem` para que el usuario la tenga en cuenta.

### Actualizar las acciones AddItem y MarkDone

Deberá usar el `UserManager` para obtener el usuario actual en los métodos de acción `AddItem` y `MarkDone`, tal como lo hizo en `Index`.

Aquí están los dos métodos actualizados:

**Controllers/TodoController.cs**

```csharp
[ValidateAntiForgeryToken]
public async Task<IActionResult> AddItem(TodoItem newItem)
{
    if (!ModelState.IsValid)
    {
        return RedirectToAction("Index");
    }

    var currentUser = await _userManager.GetUserAsync(User);
    if (currentUser == null) return Challenge();

    var successful = await _todoItemService
        .AddItemAsync(newItem, currentUser);

    if (!successful)
    {
        return BadRequest("Could not add item.");
    }

    return RedirectToAction("Index");
}

[ValidateAntiForgeryToken]
public async Task<IActionResult> MarkDone(Guid id)
{
    if (id == Guid.Empty)
    {
        return RedirectToAction("Index");
    }

    var currentUser = await _userManager.GetUserAsync(User);
    if (currentUser == null) return Challenge();

    var successful = await _todoItemService
        .MarkDoneAsync(id, currentUser);
    
    if (!successful)
    {
        return BadRequest("Could not mark item as done.");
    }

    return RedirectToAction("Index");
}
```

Ambos métodos de servicio ahora deben aceptar un parámetro `ApplicationUser`. Actualice la definición de la interfaz en `ITodoItemService`:

```csharp
Task<bool> AddItemAsync(TodoItem newItem, ApplicationUser user);

Task<bool> MarkDoneAsync(Guid id, ApplicationUser user);
```

Y por último, actualice la implementación del método del servicio en `TodoItemService`. En el método `AddItemAsync`, establece la propiedad `UserId` cuando construyas un `nuevo TodoItem`:

```csharp
public async Task<bool> AddItemAsync(
    TodoItem newItem, ApplicationUser user)
{
    newItem.Id = Guid.NewGuid();
    newItem.IsDone = false;
    newItem.DueAt = DateTimeOffset.Now.AddDays(3);
    newItem.UserId = user.Id;

    // ...
}
```

La cláusula `Where` en el método `MarkDoneAsync` también debe verificar la ID del usuario, por lo que un usuario deshonesto no puede completar las tareas de otra persona adivinando sus ID:

```csharp
public async Task<bool> MarkDoneAsync(
    Guid id, ApplicationUser user)
{
    var item = await _context.Items
        .Where(x => x.Id == id && x.UserId == user.Id)
        .SingleOrDefaultAsync();

    // ...
}
```

¡Todo listo! Intenta usar la aplicación con dos cuentas de usuario diferentes. Las tareas pendientes se mantienen privadas para cada cuenta.
