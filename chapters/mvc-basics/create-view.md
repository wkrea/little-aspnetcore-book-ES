# Crear una vista

Las vistas en ASP.NET Core se construyen usando el lenguaje de plantillas Razor, el cual combina HTML y código C#. Si haz escrito páginas usan Handlebar mustaches, ERM en Ruby On Rail or Thumeleaf en Java, ya tienes la idea básico)

La mayoría del código de las vistas es solo HTML, con ocasionales enunciados de C# agregados para extraer datos de la vista modelo que cambias a texto o HTML. Los enunciados C# es prefijados con el símbolo `@`

Las vista generada por la acción Index del "" necesita obtener datos en el View Model, (una sección de todo items u mostrando en un bonita tabla para el usuario, Por convención, las vistas va colocadas en la carpeta _Views_ en un subcarperta correspondiente al nombre del controlador. El nombre del archivo es el nombre de la acción a con un una extensión`.cshtml`,

Crea una carpeta llamada `Todo` dentro la carpeta `Views`, y agrega este archivo:
**Views/Todo/Index.cshtml**

```html
@model TodoViewModel

@{
    ViewData["Title"] = "Manage your todo list";
}

<div class="panel panel-default todo-panel">
  <div class="panel-heading">@ViewData["Title"]</div>

  <table class="table table-hover">
      <thead>
          <tr>
              <td>&#x2714;</td>
              <td>Item</td>
              <td>Due</td>
          </tr>
      </thead>

      @foreach (var item in Model.Items)
      {
          <tr>
              <td>
                <input type="checkbox" class="done-checkbox">
              </td>
              <td>@item.Title</td>
              <td>@item.DueAt</td>
          </tr>
      }
  </table>

  <div class="panel-footer add-item-form">
    <!-- TODO: Add item form -->
  </div>
</div>
```

En la parte superior del archivo, la directiva `@ model` le dice a Razor a qué modelo espera que se vincule esta vista. Se accede al modelo a través de la propiedad `Model`.

Suponiendo que hay elementos de tareas pendientes en `Model.Items`, la instrucción` foreach` se desplazará sobre cada tarea pendiente y mostrará una fila de la tabla \ (`<tr>` elemento \) que contiene el nombre y la fecha de vencimiento del elemento . También se representa una casilla de verificación que permitirá al usuario marcar el elemento como completo.

## El archivo de diseño
Quizás se pregunte dónde está el resto del HTML: ¿qué pasa con la etiqueta `<body>` o el encabezado y pie de página de la página? ASP.NET Core utiliza una vista de diseño que define la estructura base en la que se procesan todas las demás vistas. Esta almacenado en `Views/Shared/_Layout.cshtml`.

La plantilla predeterminada de ASP.NET Core incluye Bootstarp y JQuery e su archivo de Layout Puedes crear rápidamente una aplicación web, Por supuesto que puedes usar tus propias librerías CSS y Javascript u asi lo deseas.

## Personalizando la hoja de estilos

La plantilla predefinida también incluye un hoja de estilos con algunas reglas CSS básicas. La hoja de estilos es almacenada en el directorio `wwwroot/css`,Agrega unas cuantas nuevas reglas CSS al final del, archivo `site.css`:

**wwwroot/css/site.css**

```css
div.todo-panel {
  margin-top: 15px;
}

table tr.done {
  text-decoration: line-through;
  color: #888;
}
```

Puedes usar reglas CSS como estas para personalizar como se visualizan y lucen tus páginas.

ASP.NET Core y Razor pueden hacer mucho más, como vistas parciales y vistas generadas en el servidor componentes, pero un simple Layout y una vista es todo lo que necesitaras por ahora. La documentación oficial(en [https://docs.asp.net](https://docs.asp.net) de ASP.NET Core contiene muchos ejemplos si deseas aprender más.
