## Finalizando el controlador
El último paso es finalizar el código del controlador . El controlador ahora tiene un lista de tareas de la capa de servicio, y necesita poner que los items dentro de un `TodoViewModel` y enlazar este modelo a la vista creada anteriormente:

**Controllers/TodoController.cs**

```csharp
public async Task<IActionResult> Index()
{
    var items = await _todoItemService.GetIncompleteItemsAsync();

    var model = new TodoViewModel()
    {
        Items = items
    };

    return View(model);
}
```

Si no lo haz hecho ya, asegúrate que los siguientes enunciados `using` estén en la parte superior del archivo:

```csharp
using AspNetCoreTodo.Services;
using AspNetCoreTodo.Models;
```

Si estas usando Visual Studio o Visual Studio Code, el editor te sugerirá estos enunciados `using` cuando colocas el cursor en las lineas rojas.

## Probando
Para iniciar la aplicación presiona F5 (si estas usando Visual Studio o Visual Studio Code), o solo teclea `dotnet run` en la terminal. Si el código compila sin errores, el servidor empezara escuchando en el puerto 5000 de forma predeterminada.
Si tu navegador navegador no se abre de forma automática, ábrelo y navega a la dirección [http://localhost:5000/todo](http://localhost:5000/todo). Verás la vista que creaste, con los datos ficticios (por ahora) obtenidos de la base de datos .

A pesar de que es posible navegar directamente hasta `http://localhost:5000/todo`, será más conveniente agregar una liga llamada **My to-dos** a la barra de navegación. Para hacer esto, debes editar el archivo de layout.

