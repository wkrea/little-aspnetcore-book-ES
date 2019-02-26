## Pruebas de integración

En comparación con las pruebas unitarias, las pruebas de integración tienen un alcance mucho mayor. Prueba toda la pila de aplicaciones. En lugar de aislar una clase o método, las pruebas de integración aseguran que todos los componentes de su aplicación estén funcionando juntos correctamente: enrutamiento, controladores, servicios, código de base de datos, etc.

Las pruebas de integración son más lentas y más complejas que las pruebas de unitarias, por lo que es común que un proyecto tenga muchas pruebas de unitarias pequeñas pero solo un puñado de pruebas de integración.

Para probar toda la pila (incluido el enrutamiento del controlador), las pruebas de integración normalmente hacen llamadas HTTP a su aplicación como lo haría un navegador web.

Para realizar una prueba de integración, puede iniciar su aplicación y realizar solicitudes manualmente a http://localhost:5000. Sin embargo, ASP.NET Core ofrece una mejor alternativa: la clase `TestServer`. Esta clase puede alojar su aplicación durante la duración de la prueba, y luego detenerla automáticamente cuando se completa la prueba.

### Crear un proyecto de prueba

Si actualmente se encuentra en el directorio de su proyecto, `cd` sube un nivel al directorio raíz `AspNetCoreTodo`. Use este comando para crear un nuevo proyecto de prueba:

```
dotnet new xunit -o AspNetCoreTodo.IntegrationTests
```

Su estructura de directorio ahora debería verse así:

```
AspNetCoreTodo/
    AspNetCoreTodo/
        AspNetCoreTodo.csproj
        Controllers/
        (etc...)

    AspNetCoreTodo.UnitTests/
        AspNetCoreTodo.UnitTests.csproj

    AspNetCoreTodo.IntegrationTests/
        AspNetCoreTodo.IntegrationTests.csproj
```

> Si lo prefiere, puede mantener sus pruebas unitarias y pruebas de integración en el mismo proyecto. Para proyectos grandes, es común dividirlos para que sea fácil ejecutarlos por separado.

Dado que el proyecto de prueba utilizará las clases definidas en su proyecto principal, deberá agregar una referencia al proyecto principal:

```
dotnet add reference ../AspNetCoreTodo/AspNetCoreTodo.csproj
```

También deberá agregar el paquete NuGet `Microsoft.AspNetCore.TestHost`:

```
dotnet add package Microsoft.AspNetCore.TestHost
```

Elimine el archivo `UnitTest1.cs` creado por `dotnet new`. Estás listo para escribir una prueba de integración.

### Escribir una prueba de integración.

Hay algunas cosas que deben configurarse en el servidor de prueba antes de cada prueba. En lugar de abarrotar la prueba con este código de configuración, puede mantener esta configuración en una clase separada. Crea una nueva clase llamada `TestFixture`:

**AspNetCoreTodo.IntegrationTests/TestFixture.cs**

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.TestHost;
using Microsoft.Extensions.Configuration;

namespace AspNetCoreTodo.IntegrationTests
{
    public class TestFixture : IDisposable  
    {
        private readonly TestServer _server;

        public HttpClient Client { get; }

        public TestFixture()
        {
            var builder = new WebHostBuilder()
                .UseStartup<AspNetCoreTodo.Startup>()
                .ConfigureAppConfiguration((context, config) =>
                {
                    config.SetBasePath(Path.Combine(
                        Directory.GetCurrentDirectory(),
                        "..\\..\\..\\..\\AspNetCoreTodo"));
                    
                    config.AddJsonFile("appsettings.json");
                });

            _server = new TestServer(builder);

            Client = _server.CreateClient();
            Client.BaseAddress = new Uri("http://localhost:8888");
        }

        public void Dispose()
        {
            Client.Dispose();
            _server.Dispose();
        }
    }
}
```

Esta clase se encarga de configurar un `TestServer`, y ayudará a mantener las pruebas limpias y ordenadas.

Ahora estás (realmente) listo para escribir una prueba de integración. Crea una nueva clase llamada `TodoRouteShould`:

**AspNetCoreTodo.IntegrationTests/TodoRouteShould.cs**

```csharp
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Xunit;

namespace AspNetCoreTodo.IntegrationTests
{
    public class TodoRouteShould : IClassFixture<TestFixture>
    {
        private readonly HttpClient _client;

        public TodoRouteShould(TestFixture fixture)
        {
            _client = fixture.Client;
        }

        [Fact]
        public async Task ChallengeAnonymousUser()
        {
            // Arrange
            var request = new HttpRequestMessage(
                HttpMethod.Get, "/todo");

            // Act: request the /todo route
            var response = await _client.SendAsync(request);

            // Assert: the user is sent to the login page
            Assert.Equal(
                HttpStatusCode.Redirect,
                response.StatusCode);

            Assert.Equal(
                "http://localhost:8888/Account" +
                "/Login?ReturnUrl=%2Ftodo",
                response.Headers.Location.ToString());
        }
    }
}
```

Esta prueba realiza una solicitud anónima (sin iniciar sesión) a la ruta `/todo` y verifica que el navegador se redirige a la página de inicio de sesión.

Este escenario es un buen candidato para una prueba de integración, ya que involucra múltiples componentes de la aplicación: el sistema de enrutamiento, el controlador, el hecho de que el controlador está marcado con `[Authorize]`, y así sucesivamente. También es una buena prueba porque garantiza que nunca quitará accidentalmente el atributo `[Authorize]` y hará que la vista de tareas sea accesible para todos.

## Ejecutar la prueba

Ejecute la prueba en el terminal con `dotnet test`. Si todo funciona bien, verás un mensaje de éxito:

```
Starting test execution, please wait...
 Discovering: AspNetCoreTodo.IntegrationTests
 Discovered:  AspNetCoreTodo.IntegrationTests
 Starting:    AspNetCoreTodo.IntegrationTests
 Finished:    AspNetCoreTodo.IntegrationTests

Total tests: 1. Passed: 1. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 2.0588 Seconds
```


## Resumiendo

Las pruebas son un tema amplio y hay mucho más que aprender. Este capítulo no toca el código de prueba de interfaz de usuario (UI) ni el código de prueba (JavaScript), que probablemente merecen libros completos por su cuenta. Sin embargo, debe tener las habilidades y el conocimiento básico que necesita para aprender más sobre las pruebas y practicar la escritura de pruebas para sus propias aplicaciones.

La documentación de ASP.NET Core (https://docs.asp.net) y Stack Overflow son excelentes recursos para aprender más y encontrar respuestas cuando te quedas atascado.
