# Crear un proyecto de ASP.NET Core
Si todavía estas en el directorio creado para la aplicación Hello World, muévete a tu directorio Documentos o directorio inicial:

```text
cd ..
```

A continuación, crea un nueva carpeta para almacenar el proyecto completo y navega hacia el.

```text
mkdir AspNetCoreTodo
cd AspNetCoreTodo
```

A continuación, crea un nuevo proyecto con `dotnet new`, esta vez utilizaras opciones adicionales:

```text
dotnet new mvc --auth Individual -o AspNetCoreTodo
cd AspNetCoreTodo
```

Esto crea un nuevo proyecto usando la plantilla `mvc`  y agrego bits adicionales sobre autentificación y seguridad al proyecto (Cubrieré la seguridad en en capítulo _Seguridad e identidad_).

> Te preguntaras porque tener un directorio llamado `AspNetCoreTodo` dentro de otro directorio con el mismo nombre. El directorio principal o directorio raíz puede contener uno o mas directorios de proyectos . El directorio raíz es a veces llamado el directorio de la solución. Después, agregaras más directorios de proyecto lado a lado del proyecto AspNetCoreTodo. todos es una solo directorio de la solución.

Veras unos pocos archivos el la carpeta del nuevo proyecto, Una vez que abres el nuevo director , todo lo que tienes que hacer para ejecutar el proyecto es:

```text
dotnet run

Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

En lugar de imprimir en la consola y salir, este programa inicia un servidor web  y espera peticiones en el puerto 5000.

Abre tu navegador web y navega a la dirección `http://localhost:5000`. Veras la pantalla default de inicio , lo cual significa que tu proyecto esta funcionando. Cuando termines presiona Contol-C en la terminal para detener el servidor.

## Las partes de un proyecto ASP.NET Core
La plantilla `dotnet new mvc` genera un numero de archivos  y directorio para ti. Aqui están las cosas mas importantes  que obtienes fuera de la caja:

* Los archivos **Program.cs** and **Startup.cs** configuraran el servidor web  y la canalización de ASP.NET Core.La clase `Startup` es donde agregas el middleware que manipula y modifica los solicitudes de entrada y brindar cosas como archivos estáticos o paginas de errores. también en donde agregas tus propios servios al contenedor de de inyección de dependencias \(posteriormente habrá más sobre esto\). 

* Los directorios **Models**, **Views**, y **Controllers** contiene componentes de la arquitectura Modelo Vista Controlador. Exploraras  los tres en el siguiente capitulo.

* El directorio **wwwroot** contiene assets como archivos estáticos como CSS, Javascript e imágenes. Los archivos en `wwwroot` serán despachados como contenido estático y pueden ser empaquetados y minificados  automáticamente.

* El archivo **appsettings.json** contiene los parámetros de configuración de que la aplicación ASP.NET coRE cargar al inicio.Puedes almacenar aui tu las cadenas de conexión o otras coas que no quieres que estén harcodeas den el código.

## Tips para Visual Studio Code

Si estas usando Visual Studio Code por primera vez ,aui tienes un par de tips de ayuda para iniciar:

* **Abrir el directorio raíz del proyecto**: En Visual Studio Code, selecciona Archivo > Abrir carpeta. Si Visual Studio Code te solicita instalar los archivos pendientes , presionar clic es Si para agregarlos.

* **F5 para ejecutar \(y puntos de interrupción de depuración\)**: Con tu proyecto abierto, presiona F5 pra ejecutar el proyecto en el modo de depuración, Esto es lo mismo que ejecutar `dotnet run` en la linea de comandos, pero tienes el beneficio de configurar puntos de interrupción en tu código dando doble clic en el margen izquierdo:

![Punto de interrupción en Visual Studio Code](breakpoint.png)

* **Foco para corregir problemas**: Si tu código contiene linea rojos \(errores del compilador , coloca el cursos sobre el código que esta en rojo y mirar el icono del foco encendido en el margen izquierdo. el foco te sugerirá reparaciones comunes, como agregar enunciados `using` faltantes en tu código:

![Foco de Sugerencias ](lightbulb.png)

* **Compila rápidamente**: Usa el atajo `Command-Shift-B` o `Control-Shift-B` para ejecutar  la tarea de Build run la cual realiza lo mismo que `dotnet build`.

> Estos tips también aplican para visual Studio \(not Code\) en Windows Si estas usando Visual Studio, necesitaras abrir el archivo de proyecto directamente. Visual studio te solicitara guardar la el archivo de la solución, el cual debes guardar en el directorio raíz de la solución\(la primera carpeta llamado `AspNetCoreTodo`)- También puedes crear un proyecto ASP.NET Core directamente o en  Visual Studio usando la plantillas en Archivo - Nuevo Proyecto.

### Una nota acerca de Git

Si eres nuevo usando Git o Github para manejar el código fuente, ahora es buen momento para hacer un `git init` e inicia el repositorio en el directorio raiz del proyecto:

```text
cd ..
git init
```

Asegúrate que agregues un archivo  `.gitignore` que ignora las carpeta `bin` and `obj` La plantilla de Visual Studio en Github .gitignore plantilla repositorio funciona genial.

Hay mucho más que explorar, así que profundicemos e iniciemos a construir una aplicación.
