## Actualizar el contexto

Todavía no hay mucho que hacer en el contexto de la base de datos:

**Data/ApplicationDbContext.cs**

```csharp
public class ApplicationDbContext 
             : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(
        DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
        // ...
    }
}
```

Agregue una propiedad `DbSet` al `ApplicationDbContext`, justo debajo del constructor:

```csharp
public ApplicationDbContext(
    DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}

public DbSet<TodoItem> Items { get; set; }

// ...
```

Un `DbSet` representa una tabla o colección en la base de datos. Al crear una propiedad `DbSet<TodoItem>` llamada `Items`, le está diciendo a Entity Framework Core que desea almacenar las entidades `TodoItem` en una tabla llamada `Items`.

Has actualizado la clase de contexto, pero ahora hay un pequeño problema: el contexto y la base de datos ahora no están sincronizados, porque en realidad no hay una tabla `Items` en la base de datos. (Solo actualizar el código de la clase de contexto no cambia la base de datos en sí).

Para actualizar la base de datos para reflejar el cambio que acaba de realizar en el contexto, debe crear una **migración**.

> Si ya tiene una base de datos existente, busque en la web "scaffold-dbcontext database" y lea la documentación de Microsoft sobre el uso de la herramienta `Scaffold-DbContext` para realizar una ingeniería inversa de la estructura de la base de datos en el `DbContext` correspondiente y las clases de modelos automáticamente .
