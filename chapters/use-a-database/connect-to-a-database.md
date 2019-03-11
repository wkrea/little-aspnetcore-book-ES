## Conectarse a una base de datos

Hay algunas cosas que necesita para usar Entity Framework Core para conectarse a una base de datos. Ya que usó `dotnet new` y la plantilla MVC + Individual Auth para configurar su proyecto, ya los tiene:

* **Los paquetes de Entity Framework Core**. Estos se incluyen de forma predeterminada en todos los proyectos de ASP.NET Core.

* **Una base de datos** (naturalmente). El archivo `app.db` en el directorio raíz del proyecto es una pequeña base de datos SQLite creada para usted por `dotnet new`. SQLite es un motor de base de datos ligero que puede ejecutarse sin necesidad de instalar herramientas adicionales en su máquina, por lo que es fácil y rápido de usar en el desarrollo.

* **Una clase de contexto de base de datos**. El contexto de la base de datos es una clase de C# que proporciona un punto de entrada a la base de datos. Es la forma en que su código interactuará con la base de datos para leer y guardar elementos. Ya existe una clase de contexto básico en el archivo `Data/ApplicationDbContext.cs`.

* ** Una cadena de conexión **. Ya sea que se conecte a una base de datos de archivos local (como SQLite) o una base de datos alojada en otro lugar, definirá una cadena que contenga el nombre o la dirección de la base de datos a la que se conectará. Esto ya está configurado para usted en el archivo `appsettings.json`: la cadena de conexión para la base de datos SQLite es `DataSource = app.db`.

Entity Framework Core usa el contexto de la base de datos, junto con la cadena de conexión, para establecer una conexión con la base de datos. Debe indicar a Entity Framework Core qué contexto, cadena de conexión y proveedor de base de datos deben utilizar en el método `ConfigureServices` de la clase `Startup`. Esto es lo que está definido para ti, gracias a la plantilla:

```csharp
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlite(
        Configuration.GetConnectionString("DefaultConnection")));
```

Este código agrega el `ApplicationDbContext` al contenedor de servicio, y le dice a Entity Framework Core que use el proveedor de base de datos SQLite, con la cadena de conexión de la configuración (`appsettings.json`).

Como puedes ver, `dotnet new` ¡crea muchas cosas por ti! La base de datos está configurada y lista para ser utilizada. Sin embargo, no tiene tablas para almacenar elementos de tareas pendientes. Para almacenar sus entidades `TodoItem`, necesitará actualizar el contexto y migrar la base de datos.