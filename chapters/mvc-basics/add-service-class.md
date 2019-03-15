# Agregar una clase de servicio

Haz creado un modelo, una vista y un controlador. Antes de usar el modelo y la vista en el controlador, también necesitas escribir código que obtendrá la lista de tareas de los usuarios desde la base de datos.

Puedes escribir este código directamente en el controlador, pero es una mejor práctica mantener tu código separado. ¿Por qué? en una aplicación real grande, tendrás que hacer malabares con muchos asuntos:

* **Generar la Vista** y manipular datos de entrada: esto actualmente es realzado por el controlador.
* **Ejecutar las reglas de negocio**, o código que esta relacionado con el propósito y negocio de tu aplicación, en una aplicación de lista de tareas, reglas de negocio significa decisiones como configurar la fecha por omisión para la entrega o solo mostrar tareas que están incompletas. Otros ejemplo de lógica de negocio incluyen calcular el costo total con base en el precio, de los productos, razón de impuesto o verificar si algún jugador tiene puntos suficiente para subir de nivel en un juego.
* **Obtener y guardar** tareas desde la base de datos.

Una vez más es posible hacer todas estas cosas en un solo y enorme controlador, que rápidamente se convertiría en difícil de manejar y probar. En lugar es común ver aplicaciones dividen en dos o tres o más capas y tiers de tal forma que cada una maneja uno (y solo una) aspecto de la aplicación. Esto ayuda a mantener el controlador tan simple como sea posible, y hace más fácil probar y cambiar la lógica de negocio y el código de acceso a base de datos después.

Separado tu aplicación en esta forma es a veces llamada **mult-tier** o **n-tier arquitectura**. En algunos casos los tiers o capas son proyectos completamente separados. Pero otras veces i solo se referencia a como las clases son organizadas y utilizadas. Lo más importante es pensar a cerca de como dividir tu aplicación en piezas manejables y evitar tener controladores o clases enormes que intentan hacer todo.

Para este proyecto, usaras dos capa de aplicación: una **capa de presentación** compuesta de controladores y vistas que interactúan con el usuario, y una capa de servicio que combina las reglas del negocio con el código de accesos a base de datos. La capa de presentación ya existe asi que el siguiente paso es crear un servicio que maneja las reglas de negocio para las tareas y las guarda en una base da datos.

> La mayoría de los proyectos grandes usan una arquitectura 3-tier: una capa de presentación, una capa lógica de servicios y una capa de repositorio de datos. Un repositorio es una clase que que solo esta enfocada en código de acceso a base de datos (no lógica de negocios). En esta aplicación, por simplicidad, código combinaras estas en un sola capa de servicio pero siéntete libre de experimentar con diferentes formas de estructurar el código.

## Crear una interfaz

El lenguaje de programación C# incluye el concepto de **interfaces**, donde la definición de los métodos y propiedades de un objeto es separada de la clase que actualmente contiene el código de aquellos métodos y propiedades. Las interfaces hace fácil mantener tus clases desacopladas y fáciles de probar, como veras aquí (y después en el capítulo _Pruebas Automáticas)Usaras una interfaces para represente el servicio que puede interactuá con los elementos en la base de datos.

Por convención, el nombre de las interfaces tiene el prefijo "I". Crea un nuevo archivo en el directorio Services:

**Services/ITodoItemService.cs**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using AspNetCoreTodo.Models;

namespace AspNetCoreTodo.Services
{
    public interface ITodoItemService
    {
        Task<TodoItem[]> GetIncompleteItemsAsync();
    }
}
```

Nota que el espacio de nombres de este archivo es `AspNetCoreTodo.Services`. Los espacios de nombres son una forma de organizar los archivos de código .NET. y se acostumbra nombrar el espacio de nombres siguiendo el directorio en que esta almacenado del archivo (`AspNetCoreTodo.Services` para archivos en el directorio  `Services` y asi sucesivamente).

Debido a que este archivo (en el espacio de nombres `AspNetCoreTodo.Services`) hace referencia a la clase `TodoItem` (en el espacio de nombres `AspNetCoreTodo.Models`), debe incluir una declaración `using` en la parte superior del archivo para importar ese espacio de nombres. Sin la instrucción `using`, verás un error como:

```
The type or namespace name 'TodoItem' could not be found (are you missing a using directive or an assembly reference?)
```

Debido a que esta es una interfaces, no hay ningún código aquí, solo la definición (o la firma del método) `GetIncompleteItemsAsync`. Este método no requiere parámetros y regresa un objeto del tipo  `Task<TodoItem[]>`.

> Si la sintaxis parece confusa, recuerda "una Tarea(Task) que contiene un arreglo de TodoItems"

El tipo `Task` es similar un futuro o promesa, y se usa aquí porque este método sera asíncrono. En otras palabras es posible que el método no pueda ser capaz de regresar la lista de tareas pendientes de forma inmediata porque necesita primero interactuar con la base de datos primero. (Más sobre esto después).

## Crear la clase de servicio

Ahora que la interfaz esta definida, estas listo para crear la clase del servicio actual. Cubriré el código de acceso a base de datos a detalle en el capítulo _Usar una base de datos_, así que por ahora solo usaras datos falsos y siempre regresara 2 tareas definidas de forma estática;

**Services/FakeTodoItemService.cs**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using AspNetCoreTodo.Models;

namespace AspNetCoreTodo.Services
{
    public class FakeTodoItemService : ITodoItemService
    {
        public Task<TodoItem[]> GetIncompleteItemsAsync()
        {
            var item1 = new TodoItem
            {
                Title = "Learn ASP.NET Core",
                DueAt = DateTimeOffset.Now.AddDays(1)
            };

            var item2 = new TodoItem
            {
                Title = "Build awesome apps",
                DueAt = DateTimeOffset.Now.AddDays(2)
            };

            return Task.FromResult(new[] { item1, item2 });
        }
    }
}
```

La clase `FakeTodoItemService` implementa la interfaz `ITodoItemService` pero siempre regresa el mismo arreglo de dos `TodoItem`. Usaras esta para poblar el controlador y la vista y después agregaras código de bases de datos real en _Usando una base de datos_.