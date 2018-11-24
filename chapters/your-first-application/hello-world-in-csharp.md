## Hola Mundo en C# #
Antes de profundizar en ASP.NET Core, prueba creando y ejecutando una aplicación de C# simple.

Puedes hacer todo esto desde la línea de comandos. Primero, abre una Terminal (o PowerShell en Windows). Navega a la ubicación donde deseas guardar tus proyectos, tal cómo la carpeta de mis Documentos:

```
cd Documentos
```

Usa el comando `dotnet` para crear un nuevo proyecto:

```
dotnet new console -o CsharpHelloWorld
```

El comando `dotnet new` crea un nuevo proyecto de .NET con C# por omisión. El parámetro `console` selecciona una plantilla para una aplicación de consola ( un programa que emite texto en la pantalla).El parámetro `-o CsharpHelloWorld` instruye a `dotnet new` para crear una carpeta llamada `CsharpHelloWorld` para los archivos del proyecto . Cambiate a esta carpeta asi:

```
cd CsharpHelloWorld
```

`dotnet new console` crea un programa de C# básico que escribe el texto `Hello World!` en la pantalla. El programa esta compuesto por dos archivos: un archivo de proyecto(con extensión `.csproj`) y un archivo de código de C# (con extensión`.cs`). Si abres el primer archivo en un editor de texto, veras esto :

**CsharpHelloWorld.csproj**

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>

</Project>
```

El archivo de proyecto esta baso en XML y define algunos metadatos sobre el proyecto. Después, cuando referencias otros paquetes, aquellos serán listados aquí (de forma similar a un un archivo `package.json` para npm). No necesitarás editar esté archivo de forma manual muy seguido.

**Program.cs**

```csharp
using System;

namespace CsharpHelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

El método `static void Main` es el punto de entrada de un programa de C#, y por convención esta colocado en una clase (un tipo de código estructura o modulo) llamado `Program`. El enunciado `using` al inicio importa las clases incluidas en espacio de nombres `System` desde .NET y las hace disponibles en el código en tú clase.

Dentro de la carpeta del proyecto, usa `dotnet run` para ejecutar el programa. Veras que la salida se escribe en la consola después que el código compila:

```text
dotnet run

!Hola Mundo!
```

!Esto es todo lo necesario para crear y ejecutar un programa en .NET! Enseguida, harás lo mismo para una aplicación ASP.NET Core.