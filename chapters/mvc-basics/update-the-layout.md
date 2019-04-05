## Actualizar el layout
El archivo de layout en `Views/Shared/_Layout.cshtml` contiene el código HTML base para cada vista. Este incluye la barra de navegación, la cual es generada en la parte superior de cada página.

Para agregar un nuevo elemento la barra de navegación, encuentra el código HTML para los elementos existentes de la barra de navegación.

**Views/Shared/_Layout.cshtml**

```html
<ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">
        Home
    </a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="About">
        About
    </a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Contact">
        Contact
    </a></li>
</ul>
```

Agrega tu propio elemento que apunta hacia el controlador `Todo` en lugar de `Home`:

```html
<li>
    <a asp-controller="Todo" asp-action="Index">My to-dos</a>
</li>
```

Los atributos `asp-controller` y `asp-action` del elemento `<a>` se llaman **tag helpers**. Antes de generar la vista, ASP.NET Core reemplaza los *tag helpers* por atributos HTML reales. En este caso, se genera una URL para la ruta `/Todo/Index` y se agrega al elemento <a> como un atributo href. Esto significa que no tiene que codificar la ruta manualmente al controlador `TodoController`. En su lugar, ASP.NET Core lo genera automáticamente.

> Si haz utilizado Razor en ASP.NET 4.X, notarás algunos cambios de sintaxis. En lugar de usar `@Html.ActionLink()` para generar un liga hacia un acción, tag helpers son ahora la forma recomendada de crear link en tus vistas. Tag helpers son útiles para los formularios, también (verás porque un el siguiente capítulo). Puedes aprender más hacer de otros tag helpers en la documentación en [https://docs.asp.net](https://docs.asp.net).
