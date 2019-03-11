## Pruebas unitarias

Las pruebas unitarias son pruebas pequeñas y cortas que verifican el comportamiento de un solo método o clase. Cuando el código que está probando se basa en otros métodos o clases, las pruebas unitarias se basan en **simulardores** de esas otras clases para que la prueba solo se enfoque en una cosa a la vez.

Por ejemplo, la clase `TodoController` tiene dos dependencias: un `ITodoItemService` y el `UserManager`. El `TodoItemService`, a su vez, depende del `ApplicationDbContext`. (La idea de que puede dibujar una línea desde `TodoController`>`TodoItemService`>`ApplicationDbContext` se llama **gráfico de dependencia**).

Cuando la aplicación se ejecuta normalmente, el sistema de inyección de dependencias y el contenedor de servicios de ASP.NET Core inyecta cada uno de esos objetos en el gráfico de dependencia cuando se crea el `TodoController` o el `TodoItemService`.

Cuando escribe una prueba de unitaria, por otro lado, tiene que manejar usted mismo el gráfico de dependencia. Es típico proporcionar versiones solo de prueba o "simuladas" de esas dependencias. Esto significa que puede aislar solo la lógica en la clase o el método que está probando. (¡Esto es importante! Si está probando un servicio, no quiere **también** estar escribiendo accidentalmente en su base de datos).

### Crear un proyecto de prueba

Es una buena práctica crear un proyecto separado para sus pruebas, para que se mantengan separados del código de su aplicación. El nuevo proyecto de prueba debe vivir en un directorio que esté al lado (no dentro) del directorio de su proyecto principal.

Si actualmente se encuentra en el directorio de su proyecto, `cd` sube un nivel. (Este directorio raíz también se llamará `AspNetCoreTodo`). Luego use este comando para crear un nuevo proyecto de prueba:

```
dotnet new xunit -o AspNetCoreTodo.UnitTests
```

xUnit.NET es un marco de prueba popular para el código .NET que se puede usar para escribir pruebas de unitarias y de integración. Como todo lo demás, es un conjunto de paquetes NuGet que se pueden instalar en cualquier proyecto. La plantilla `dotnet new xunit` ya incluye todo lo que necesitas.

Su estructura de directorio ahora debería verse así:

```
AspNetCoreTodo/
    AspNetCoreTodo/
        AspNetCoreTodo.csproj
        Controllers/
        (etc...)

    AspNetCoreTodo.UnitTests/
        AspNetCoreTodo.UnitTests.csproj
```

Como el proyecto de prueba utilizará las clases definidas en su proyecto principal, deberá agregar una referencia al proyecto `AspNetCoreTodo`:

```
dotnet agregar referencia ../AspNetCoreTodo/AspNetCoreTodo.csproj
```

Elimine el archivo `UnitTest1.cs` que se crea automáticamente. Estás listo para escribir tu primera prueba.

> Si está utilizando Visual Studio Code, es posible que deba cerrar y volver a abrir la ventana de Visual Studio Code para la compleción del código funcione en el nuevo proyecto.

### Escribe una prueba de servicio

Eche un vistazo a la lógica en el método `AddItemAsync()` del `TodoItemService`:

```csharp
public async Task<bool> AddItemAsync(
    TodoItem newItem, ApplicationUser user)
{
    newItem.Id = Guid.NewGuid();
    newItem.IsDone = false;
    newItem.DueAt = DateTimeOffset.Now.AddDays(3);
    newItem.UserId = user.Id;

    _context.Items.Add(newItem);

    var saveResult = await _context.SaveChangesAsync();
    return saveResult == 1;
}
```

Este método toma una serie de decisiones o suposiciones sobre el nuevo elemento (en otras palabras, realiza la lógica de negocios en el nuevo elemento) antes de que realmente lo guarde en la base de datos:

* La propiedad `UserId` debe establecerse a la ID del usuario
* Los nuevos elementos siempre deben estar incompletos (`IsDone = false`)
* El título del nuevo elemento debe copiarse de `newItem.Title`
* Las nuevos tareas siempre deben vencer dentro de 3 días

Imagínese si usted o alguien más reformuló el método `AddItemAsync()` y se olvidó de parte de esta lógica de negocios. ¡El comportamiento de su aplicación podría cambiar sin que usted se dé cuenta! Puede evitar esto escribiendo una prueba que verifique que esta lógica de negocios no haya cambiado (incluso si la implementación interna del método cambia).

> Puede parecer poco probable ahora que pueda introducir un cambio en la lógica empresarial sin darse cuenta, pero es mucho más difícil hacer un seguimiento de las decisiones y suposiciones en un proyecto grande y complejo. Cuanto más grande sea su proyecto, más importante es tener controles automáticos que aseguren que nada haya cambiado.

Para escribir una prueba unitaria que verifique la lógica en `TodoItemService`, cree una nueva clase en su proyecto de prueba:

**AspNetCoreTodo.UnitTests/TodoItemServiceShould.cs**

```csharp
using System;
using System.Threading.Tasks;
using AspNetCoreTodo.Data;
using AspNetCoreTodo.Models;
using AspNetCoreTodo.Services;
using Microsoft.EntityFrameworkCore;
using Xunit;

namespace AspNetCoreTodo.UnitTests
{
    public class TodoItemServiceShould
    {
        [Fact]
        public async Task AddNewItemAsIncompleteWithDueDate()
        {
            // ...
        }
    }
}
```

> Hay muchas maneras diferentes de nombrar y organizar pruebas, todas con diferentes pros y contras. Me gusta después de fijar mis clases de prueba con `Debería` para crear una oración legible con el nombre del método de prueba, ¡pero puede usar su propio estilo!

El atributo `[Fact]` proviene del paquete xUnit.NET, y marca este método como un método de prueba.

El `TodoItemService` requiere un `ApplicationDbContext`, que normalmente está conectado a su base de datos. No querrás usar eso para las pruebas. En su lugar, puede usar el proveedor de base de datos en memoria de Entity Framework Core en su código de prueba. Como toda la base de datos existe en la memoria, se borra cada vez que se reinicia la prueba. Y, dado que es un proveedor adecuado de Entity Framework Core, ¡TodoItemService no notará la diferencia!

Use un `DbContextOptionsBuilder` para configurar el proveedor de la base de datos en memoria, y luego haga una llamada a `AddItemAsync () `:

```csharp
var options = new DbContextOptionsBuilder<ApplicationDbContext>()
    .UseInMemoryDatabase(databaseName: "Test_AddNewItem").Options;

// Set up a context (connection to the "DB") for writing
using (var context = new ApplicationDbContext(options))
{
    var service = new TodoItemService(context);

    var fakeUser = new ApplicationUser
    {
        Id = "fake-000",
        UserName = "fake@example.com"
    };

    await service.AddItemAsync(new TodoItem
    {
        Title = "Testing?"
    }, fakeUser);
}
```

La última línea crea un tarea llamado "¿Pruebas?", Y le dice al servicio que lo guarde en la base de datos (en memoria).

Para verificar que la lógica de negocio funcionó correctamente, escriba un código más debajo del bloque `using` existente:

```csharp
// Use a separate context to read data back from the "DB"
using (var context = new ApplicationDbContext(options))
{
    var itemsInDatabase = await context
        .Items.CountAsync();
    Assert.Equal(1, itemsInDatabase);
    
    var item = await context.Items.FirstAsync();
    Assert.Equal("Testing?", item.Title);
    Assert.Equal(false, item.IsDone);

    // Item should be due 3 days from now (give or take a second)
    var difference = DateTimeOffset.Now.AddDays(3) - item.DueAt;
    Assert.True(difference < TimeSpan.FromSeconds(1));
}
```

La primera afirmación es una comprobación de validez: nunca debe haber más de un elemento guardado en la base de datos en memoria. Suponiendo que eso sea cierto, la prueba recupera el elemento guardado con `FirstAsync` y luego afirma que las propiedades están establecidas en los valores esperados.


> Tanto las pruebas de unitarias como las de integración generalmente siguen el patrón AAA (Arrange-Act-Assert): los objetos y los datos se configuran primero, luego se realiza alguna acción y, finalmente, la prueba verifica (Assert) que ocurrió el comportamiento esperado.

Confirmar un valor de fecha y hora es un poco complicado, ya que la comparación de dos fechas para la igualdad fallará incluso si los componentes de milisegundos son diferentes. En su lugar, la prueba verifica que el valor `DueAt` esté a menos de un segundo del valor esperado.

### Ejecutar la prueba

En la terminal, ejecute este comando (asegúrese de que todavía esté en el directorio `AspNetCoreTodo.UnitTests`):
```
dotnet test
```

El comando `test` escanea el proyecto actual en busca de pruebas (en este caso marcadas con los atributos `[Fact]`), y ejecuta todas las pruebas que encuentra. Verás una salida similar a:

```
Starting test execution, please wait...
 Discovering: AspNetCoreTodo.UnitTests
 Discovered:  AspNetCoreTodo.UnitTests
 Starting:    AspNetCoreTodo.UnitTests
 Finished:    AspNetCoreTodo.UnitTests

Total tests: 1. Passed: 1. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 1.9074 Seconds
```

Ahora tiene una prueba que proporciona cobertura de prueba del `TodoItemService`. Como desafío adicional, intente escribir pruebas unitarias que aseguren:

* El método `MarkDoneAsync()` devuelve falso si se pasa un ID que no existe
* El método `MarkDoneAsync()` devuelve true cuando se marca una tarea como completa.
* El método `GetIncompleteItemsAsync()` devuelve solo las tareas que pertenecen a un usuario en particular