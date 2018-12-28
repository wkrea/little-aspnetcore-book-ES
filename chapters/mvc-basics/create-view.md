# Crear una vista

Las vistas en ASP.NET Core se construyen usando el lenguaje de plantillas Razor, el cual combina HTML y código C\#. Si haz escrito paginas usaon Handlebar mustaches, ERM en Ruby On Rail or Thumeleaf enJava , ya tienes la idea básico)

La mayoría del código de las vistas es solo HTML , con ocacionales enuciados de C# agregosp para extraer data del vista modelo que ambias a testo o HTML. Los enucniado C# es prefijados con el simbolo `@`

Las vista renderb por la acción indes del "" necesita obtener datos en el vier mode , \( una seciende de todo itemns u mostroanso en un bonita tabla para el usuario, Por convención , las vistas a con co licasl en la capreta vVIes en un subcarperta correspondiente al nombre del controlador. El nombre del archivo es el nombre de la acción a con un una extensión`.cshtml`,

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

At the very top of the file, the `@model` directive tells Razor which model to expect this view to be bound to. The model is accessed through the `Model` property.

Assuming there are any to-do items in `Model.Items`, the `foreach` statement will loop over each to-do item and render a table row \(`<tr>` element\) containing the item's name and due date. A checkbox is also rendered that will let the user mark the item as complete.

## The layout file
You might be wondering where the rest of the HTML is: what about the `<body>` tag, or the header and footer of the page? ASP.NET Core uses a layout view that defines the base structure that every other view is rendered inside of. It's stored in `Views/Shared/_Layout.cshtml`.

La plantilla defailt de ASP.NET Core incluete Bootstarp y JQuery e su archovi de llayput Puedes crar rapidamente una apliacion cion web , Por supuesto que puedes usar tus propias libreasi CSS y Javascrio u asi lo deseas.

## Personalizando la hoja de estilos

La plantillla defialt tamben incluye un hoja de estolos con alginas reglas CSS basicoas-La hoja de esticos es almacenad en el directorio ,Agrefa unas cuiante nievas reglas CSS al finla e, archivo `site.css`:

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

Puedes usar reglas CSS como estas para personalizar como se visualizam y lucen tus paginas.

ASP.NET Core y Razor pueden hacer mucho mas, como vistas parciales y vistas server-rendered componetes, pero un simple latou y na vista es todo lo que necesiaras por ahora. La documentacion oficial\(at [https://docs.asp.net](https://docs.asp.net)\) de ASP.NET coRE contiene muchos ejemplos si deseas aprender mas.
