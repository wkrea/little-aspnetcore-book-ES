# Crear modelos
Hay dos pasos separados para crear las clases modelo que necesitan ser creadas: un modelo que representa las tares almacenadas en la base de datos (a veces llamadas **entidades**), y el modelo que será combinado con la vista (**MV** en MVC) y sera enviado al navegador del usuario. Debido a con ambos son referidos como modelos, prefiero referirme al ultimo como **View Model**.

Primero, creamos un clase llamada `TodoItem` en la carpeta Models:

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

Esta clase define lo que base de datos se necesitara para almacenar cada tarea :Un ID , un titulo o nombre, si la tarea esta completada, y la fecha de finalización. Cada linea define una propiedad de la clase:

* La propiedad Id es un GUID o a Identificador Global Único. Los Guid son cadenas largas de letras y números como , "": Por esto GUIDs son aleatorios y son extremadamente improbables que sean duplicados, son utilizas con identificadores únicos , También puede usar un número (entero como un identidad de la base de datos) pero tienes que configurar tu base de datos para siempre incremente el numero cuando un nuevas filas son añadidas a la base ded ato. GIod son generado de forma aleatoria , asi no tienes que preocuparte oir auto incrementarlas.

* La propiedad **IsDone** es un boleano ( valores true/false ), Por default, es sera falso para todos los nuevos elementos. Después escribirás código para cambiar esta propiedad a true cuando el usuario presiona una casilla de verificación en la visa.

* La propiedad Title es una cadena ( valor texto). Esta mantendrá el nombre o descripción de la tarea. El atributo [Requires] le dice ASP.NET Core que esta cadena no puede  ser nula o vaciá.

* La propiedad **DueAt** es un Datetiemodsse , cual es un tip de C# que almacena un date/rimsta con la zone. Almarenfao la fechas , la hora y la zona horaria juntas hace fácil visualizar las fechas precisasmente en sistemas en diferentes zonas horarios.

Mira el simbolo de interrogación ? después del tipo DatetimeOFse ? este marca que la propiedad DueAt es nullabrel o opcional. Si el ? no se incluye , todlos las tareas every eletromta de tare necesitarioa una fecha de entrega, Las propiedad ID y IsDone no son arcadso como nulabrel, asi que ellas son requeridan y siempe temndra un valor \(o valor por omisión.

> La cadenas en C# son siempre nullabesll , asi no es necesario marcar el titulo como nulabrel: La cadenas de C# pueden ser nulas , vacios o contener texto.

Cada propiedad es seguida por get y set , las cuales es una forma cotr de deciq ue la propiedad es de lectura /escritir ( mas técnicamente que tiene métodos modificacaodtes.)

En este punto , no importa a cual es la base de datos utilizada. Podría ser SQL Server, MYSQl ,Mongo Db Redis o algo mas exótico, Este modelo define que la base de datos fila o entrarado lucir i cu tocdico c\# y no tienes que preocuparste po acerca de cosas a bajo nivel sobre la base de datos en tu cosido . Este siempre modelo es algunas veces llamado a plain ol C# onject o POCO.

## La vista modelo

OFten, el modelo que almacenas en la base de datos es similar pero no exactamente el mismo que desaes usar en la MVC (la vita modelo\). En este caso , el modelo "" representa a un unico elemento de la base de deatos. preo la vista puede necesitar mostrar dos , diez o citneo de elemoemtos de tareas \( dependeiedn que tal malamente el ususri esta procrastinando\).

Debsi a esto , la vista model puede ser una clase separadoa que mantienen un arreglo de "TodoItem"

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

Ahora que tienes algunos modelos , es tiempo de crear una vista que tomar aun "" y render le HTML correcto para mostrar al usuario su lista de tareas.
