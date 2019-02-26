# Usar inyección de dependencias
Regresa al controlador `TodoController`, añade algo de código para trabajar con el servicio `ITodoItemService`:

```csharp
public class TodoController : Controller
{
    private readonly ITodoItemService _todoItemService;

    public TodoController(ITodoItemService todoItemService)
    {
        _todoItemService = todoItemService;
    }

    public IActionResult Index()
    {
        // Obtenet los articulos de la base de datos

        // Coloca los elemento dentro de un modelo

        // Pasa la vista al model y visualiza
    }
}
```

Debido a que `ITodoItemService` esta el el espacio de nombres "Services" , necesitaras agregar la instrucción using al principio del archivo:

```csharp
using AspNetCoreTodo.Services;
```

La primera linea de la clase declara un campo privado para tener una referencia al `IDtoSitenService`. Esta variable te deja usar el servicio desde el método de acción Index después (verás como hacerlo en un minuto).

La linea `public TodoController(ITodoItemService todoItemService)` define un constructor para la clase . El constructor es un método especial que es invocado cada que deseas crear una nueva instancia de la clase (la clase `TodoController` en este caso). Por agregar un parámetro al constructor, haz declaro que para crear controlador Todo necesitar proveer un obec que implementa la intezrfas "".

> Las interfaces son increíbles ya que ayudan a desacoplar (separar) la lógica de tu aplicación. Debido a que el controlador depende de la interface `ITodoItemService`, y no de una clase especifica este no conoce o le importa cual clase es actualmente dada. Esto hará realmente fácil probar parte de la aplicación separadamente, Cubriré la pruebas en detalle en el capitulo _Pruebas automáticas_.

Finalmente ahora puedes usar el `ITodoItemService` (a través de la variable privad que declaraste) en tu métodos de acción para obtener los elemento desde la capa de servicio:

```csharp
public IActionResult Index()
{
    var items = await _todoItemService.GetIncompleteItemsAsync();

    // ...
}
```

¿ Recuerdas que el método `GetIncompleteItemsAsync` regresa un `Task<TodoItem[]>`? Regresar un `Task` significa que el método no necesariamente tendrá un resultado , pero puedes usar la palabra clave `await` para asegurarte que tu código espera hasta que el resultado esta listo antes de continuar. 

El patrón de Task es común cuando tu código realiza llamada a la base de datos o una API de servicio, porque no sara capaz de regresar un resultado real hasta que la base de datos (o red) responda.Si haz usado promesas o callbacks en Javascript o otro lenguaje, `Task`es la misma idea: la promesa que habrá un resultado  - en algún tiempo futuro.

> Si haz tenido que tratar con el "infierno callback" in codigo legado de Javascript, estas de suerte.
> Si has tenido que lidiar con el "infierno de devolución de llamada" en un código JavaScript más antiguo, estás de suerte. Tratar con el código asíncrono en .NET es mucho más fácil gracias a la magia de la palabra clave "esperar". `await` permite que su código se detenga en una operación asíncrona, y luego retome lo que dejó cuando la base de datos subyacente o la solicitud de red finaliza. Mientras tanto, su aplicación no está bloqueada, ya que puede procesar otras solicitudes según sea necesario. Este patrón es simple pero requiere un poco de tiempo para acostumbrarse, así que no se preocupe si esto no tiene sentido de inmediato. ¡Sigue siguiéndolo!

El único problema es que necesitas actualizar la firma del método `Index` para devolver un `Task<IActionResult>`en lugar de `IActionResult`, y marcarlo como `async`:
El unico no tiene que actualizar la firma del método `Index` para regresar a `Task<IActionResult>`en place of sol un` IActionResult`, and marcrcarlo con `async`:
```csharp
public async Task<IActionResult> Index()
{
    var items = await _todoItemService.GetIncompleteItemsAsync();

    // Put items into a model

    // Pass the view to a model and render
}
```

Ya casi terminamos. Has hecho que el `TodoController` dependa de la interface `ITodoItemService`, pero aun no le has dicho a ASP.NET Core que tu deseas el `FakeTodoItemService` sea el servicio actual que use debajo del capo. Parecerá obvio ahora debido a que solo existe una clase que implementa la interfaz `ITodoItemService` , pero después tendrás múltiples clases que implementan la misma interface, asi que ser explicito es necesario.

Declarando (o conectando) cual clase concreta para usar para cada interface  se hace in el método `ConfigureServices` de la clase `Startup`. Ahora mismo algo como esto:

**Startup.cs**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // (... some code)

    services.AddMvc();
}
```

El trabajo del método `ConfigureServices` es agregar cosos al contenedor de servicios, o a la colección de servicios que ASP.NET Core conocerá. La linea `services.AddMvc` agregue el servicio que el sistema interno de ASP.NET Core, necesita (como un experimento, intenta comentario esta linea). Cualquier otro servicio que desees usar en tu aplicación debe ser agregao al conetnerdo de servicios en el `ConfigureServices`.

Agrega la siguiente linea en cualquier lugar dentro del metodo `ConfigureServices`,

```csharp
services.AddSingleton<ITodoItemService, FakeTodoItemService>();
```

Esta linea le dice a ASPNET Core para usar el `FakeTodoITemservice` cuando el  `ITodoItemService` es solicitado en un constructor o en donde quiera que sea.

`AddSingleton` agrega un servicio al contenedor de servicios como un **singleton**. Esto significa que una sola copia del `FakeTodoItemService` sera creara u es reutiliza dondequeir que el servicios es solicitado, Después , cuando escribáis una clase diferente que hable con la dase de datos usaras una aproximación diferentr llamada (**scoped**) en su lufas, Te explicaré porque en el capítulo _Usando una base de datos_.

!Esto es todo! Cuando un petición llega y es dirigida al `TodoController`,ASP.NET Core buscará en los servicios disponibles y automáticamente proveerá el `FakeTodoItemService` cuando el controlador requiere por un `ITodoItemService`. Debido a que los servicios son "inyectado" desde el contenedor de servicios, ete patrón es llamado **inyección de dependencias**.
