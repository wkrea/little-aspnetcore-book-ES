## Completa los elementos con una casilla de verificación

Agregar tareas a tú lista de tareas es genial, pero eventualmente necesitaras también completar las cosas. En la vista `Views/Todo/Index.cshtml`, una casilla de verificación es, mostrada para cada tarea:

```html
<input type="checkbox" class="done-checkbox">
```

Presionando la casilla de verificación no hace nada aun. Al igual que en el capítulo anterior, agregaras este comportamiento usando formularios y acciones. En este caso necesitaras un pequeño código en Javascript.


### Agregar elementos al formulario de la vista

Primero, actualiza la vista y encierra cada casilla de verificación con un elemento `<form>`. Después, agregar un elemento oculto que contenga el ID de la tarea:

**Views/Todo/Index.cshtml**

```html
<td>
    <form asp-action="MarkDone" method="POST">
        <input type="checkbox" class="done-checkbox">
        <input type="hidden" name="id" value="@item.Id">
    </form>
</td>
```

Cuando el bucle `foreach` se ejecuta en la vista e imprime una fila para cada tarea pendiente, existirá una copia de este formulario en cada fila. La entrada oculta que contiene el ID de la tarea a realizar permite que el código de su controlador indique qué casilla se marcó. (Sin él, podría indicar que se marcó *alguna* casilla, pero no cuál.)

Si ejecutas la aplicación ahora mismo, las casillas de verificación aun no hacen nada, porque no hay un botón para submit para decir al navegador para crear una solicitud POST con los datos del formulario. Puedes agregar un botón de submit bajo cada casilla de verificación pero esto seria una mala experiencia de usuario. Idealmente dando clic en una casilla de verificación debería envía el formulario. Puedes lograrlo agregando algo de código de JavaScript.


### Agregar código Javascript

Busca el archivo `site.js` en el directorio `wwwroot/js` y agrega este código: 

**wwwroot/js/site.js**

```javascript
$(document).ready(function() {

    // Wire up all of the checkboxes to run markCompleted()
    $('.done-checkbox').on('click', function(e) {
        markCompleted(e.target);
    });
});

function markCompleted(checkbox) {
    checkbox.disabled = true;

    var row = checkbox.closest('tr');
    $(row).addClass('done');

    var form = checkbox.closest('form');
    form.submit();
}
```

Este código primero usa jQuery (a una librería de apoyo en JavaScript) para adjuntar algo de código al evento `click` de todos las casillas de verificación sobre la página con la clase CSS `done-checkbox`. Cuando una casilla de verificación es presionada, la función `markCompleted()` es ejecutada.

La función `markCompleted()` hace algunas cosas:
* Agrega el atributo `disabled` a las casillas de verificación así estas no pueden ser selecionadas otra vez
* Agrega la clase CSS `done` a la fila padre que contiene la casilla de verificación, la cual cambia la forma que la final luce basada en las reglas CSS en el archivo `style.css`
* Enviar el formulario

Esto toma responsabilidad del la vista y el código del lado del cliente. Ahora es tiempo de agregar una nueva acción

### Agregar una acción al controlador

Como haz probablemente adivinado, necesitas agregar una acción llamada `MarkDone` en el controlador `TodoController`:

```csharp
[ValidateAntiForgeryToken]
public async Task<IActionResult> MarkDone(Guid id)
{
    if (id == Guid.Empty)
    {
        return RedirectToAction("Index");
    }

    var successful = await _todoItemService.MarkDoneAsync(id);
    if (!successful)
    {
        return BadRequest("Could not mark item as done.");
    }

    return RedirectToAction("Index");
}
```

Vayamos a través de cada línea de este método de acción. Primero, el método acepta un parámetro `Guid` llamado` id` en la firma del método. A diferencia de la acción `AddItem`, que utiliza un modelo y un modelo de enlace / validación, el parámetro `id` es muy simple. Si los datos de la solicitud entrante incluyen un campo llamado `id`, ASP.NET Core intentará analizarlo como una guía. Esto funciona porque el elemento oculto que agregó al formulario de casilla de verificación se llama `id`.

Como no está utilizando el enlace de modelo, no hay un `ModelState` para verificar la validez. En su lugar, puede verificar el valor GUID directamente para asegurarse de que sea válido. Si, por algún motivo, el parámetro `id` en la solicitud faltaba o no podía analizarse como guid, `id` tendrá un valor de `Guid.Empty`. Si ese es el caso, la acción le dice al navegador que redirija a `/Todo/Index` y actualice la página.

A continuación, el controlador debe llamar a la capa de servicio para actualizar la base de datos. Esto será manejado por un nuevo método llamado `MarkDoneAsync` en la interfaz `ITodoItemService`, que devolverá verdadero o falso dependiendo de si la actualización tuvo éxito:

```csharp
var successful = await _todoItemService.MarkDoneAsync(id);
if (!successful)
{
    return BadRequest("Could not mark item as done.");
}
```

Finalmente, si todo se ve bien, el navegador se redirige a la acción `/Todo/Index` y la página se actualiza.

Con la vista y el controlador actualizados, todo lo que queda es agregar el método de servicio faltante.

### Agregar un método de servicio

Primero, agregar `MarkDoneAsync` a la definición de la interface:

**Services/ITodoItemService.cs**

```csharp
Task<bool> MarkDoneAsync(Guid id);
```

Después, agrega la implementación concreta al servicio `TodoItemService`: 

**Services/TodoItemService.cs**

```csharp
public async Task<bool> MarkDoneAsync(Guid id)
{
    var item = await _context.Items
        .Where(x => x.Id == id)
        .SingleOrDefaultAsync();

    if (item == null) return false;

    item.IsDone = true;

    var saveResult = await _context.SaveChangesAsync();
    return saveResult == 1; // One entity should have been updated
}
```

Este método usa Entity Framework Core y `Where()` para encontrar una tarea por ID en la base de datos. El método `SingleOrDefaultAsync()` regresara una tarea o `null` si esta no es encontrada.

Una vez que estas seguro que el `item` no es nulo, es una simple cuestión de configurar la propiedad `IsDone`:

```csharp
item.IsDone = true;
```

Cambiando la propiedad solo afecta a la copia local de la tarea hasta que el método `SaveChangesAsync()` es llamada para guardar el cambio en la base de datos. `SaveChangesAsync()` regresa un numero que indica cuántas entidades fueron actualizas durante la operación de guardar. En este caso, sera o 1 (la tarea fue actualizada) o (algo malo sucedió).

### Probando

Ejecuta la aplicación y checa algunas tareas de la lista. Refrescar la página y ellas desaparecerán completamente, porque el filtro `Where()` aplicado en el método `GetIncompleteItemsAsync()`.
Ahora mismo, la aplicación contiene una sola, lista de tareas compartida. Seria mucho más util si mantuviera registros de una lista de tareas individual para cada usuario. En el siguiente capítulo, agregarás inicio de sesión y características de seguridad al proyecto.