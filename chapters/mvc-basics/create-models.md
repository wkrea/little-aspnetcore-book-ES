# Crear modelos
Hay dos clases modelo diferentes que necesitan ser creadas: un modelo que representa las tareas almacenadas en la base de datos (a veces llamadas **entidades**) y el modelo que será combinado con la vista (**MV** en MVC) y sera enviado al navegador del usuario. Debido a que ambos son referidos como modelos, prefiero referirme al último como **View Model**.

Primero, crea un clase llamada `TodoItem` en la carpeta Models:

**Models/TodoItem.cs**

```csharp
using System;
using System.ComponentModel.DataAnnotations;

namespace AspNetCoreTodo.Models
{
    public class TodoItem
    {
        public Guid Id { get; set; }

        public bool IsDone { get; set; }

        [Required]
        public string Title { get; set; }

        public DateTimeOffset? DueAt { get; set; }
    }
}
```

Esta clase define lo que base de datos necesitara para almacenar cada tarea :Un ID, un titulo o nombre, si la tarea esta completada y la fecha de termino. Cada linea define una propiedad de la clase:

* La propiedad **Id** es un GUID o un Identificador Global Único. Los Guid son cadenas largas de letras y números como por ejemplo, `43ec09f2-7f70-4f4b-9559-65011d5781bb`. Debido a que los GUIDs son aleatorios y extremadamente improbables que sean repitan, son utilizados frecuentemente con identificadores únicos. También puede usar un número (entero) como un identificador pero tienes que configurar tú base de datos para que siempre incremente el número cuando un nuevas filas son añadidas a la base de datos. Los Id son generados de forma aleatoria, así que no tienes que preocuparte de generarlos.

* La propiedad **IsDone** es un booleano ( valores true/false ), De forma predeterminada, sera falso para todos los nuevos elementos. Después escribirás código para cambiar esta propiedad a true cuando el usuario presiona una casilla de verificación en la vista.

* La propiedad **Title** es una cadena (valor texto). Esta mantendrá el nombre o descripción de la tarea pendiente. El atributo `[Required]` le dice ASP.NET Core que esta cadena no puede ser nula o vaciá.

* La propiedad **DueAt** es un `DateTimeOffset`, el cual es un tipo de C# que almacena un fecha/hora con la diferencia de horario con el UTC. Guardar la fecha, la hora y la zona horaria juntas hace fácil visualizar las fechas precisamente en sistemas en diferentes zonas horarios.

Nota que el símbolo de interrogación `?` después del tipo `DateTimeOffset?` este marca que la propiedad DueAt es nullable u opcional. Si el `?` no se incluye, todos los las tareas pendientes necesitarían una fecha de entrega. Las propiedad ID y IsDone no son marcadas como nullables, asi que ellas son requeridas y siempre tendrá un valor (o valor por omisión).

> La cadenas en C# son siempre nullables, así que no es necesario marcar el titulo como nullable: Las cadenas de C# pueden ser nulas, vacíos o contener texto.

Cada propiedad es seguida por `get; set;`, las cuales es una forma corta de decir que la propiedad es de lectura/escritura (más técnicamente que tiene métodos modificadores.)

En este punto, no importa a cual es la base de datos utilizada. Podría ser SQL Server, MySQL, Mongo Db, Redis o algo más exótico. Este modelo define como lucirá en C# una fila o entrada en la base de datos así no tienes que preocuparte acerca de los detalles sobre la base de datos en el código de tú aplicación. Este simple estilo de modelo es algunas veces llamado a POCO (Plain Old C# Object por sus siglas en ingles).

## La vista modelo

Frecuentemente, el modelo que almacenas en la base de datos es similar pero no exactamente el mismo que deseas usar en la MVC (la vista modelo). En este caso, el modelo `TodoItem` representa a un único elemento de la base de datos. Pero la vista puede necesitar mostrar dos o cientos tareas pendientes (dependiendo que tal malamente el usuario esta procrastinando).

Debido a esto, la vista modelo puede ser una clase separada que mantienen un arreglo de `TodoItem`

**Models/TodoViewModel.cs**

```csharp
namespace AspNetCoreTodo.Models
{
    public class TodoViewModel
    {
        public TodoItem[] Items { get; set; }
    }
}
```

Ahora que tienes algunos modelos, es tiempo de crear una vista que usa un `TodoViewModel` y generará el HTML correcto para mostrar al usuario su lista de tareas pendientes.
