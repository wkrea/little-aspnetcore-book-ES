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

Esto crea un nuevo proyecto usando la plantilla `mvc`  y agrego bits adicionales sobre autentificación y seguridad al proyecto \(Cubrieré la seguridad en en capítulo _Seguridad e identidad_\).

> Te preguntaras porque tener un directorio llamado `AspNetCoreTodo` dentro de otro directorio con el mismo nombre. El directorio principal o directorio raíz puede contener uno o mas directorios de proyectos . El directorio raíz es a veces llamado el directorio de la solución. Después, agregaras más directorios de proyecto lado a lado del proyecto AspNetCoreTodo. todos es una solo directorio de la solución.

Veras unos pocos archivos el la carpeta del nuevo proyecto, Una vez que abres el nuevo director , todo lo que tienes que hacer para ejecutar el proyecto es:

```text
dotnet run

Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

En lugar de inmprimir en la consola y salur , este programa inicia un servidor web  y espera peticiones en el puerto 5000.

Abre tu navegador web y navega a la direccion `http://localhost:5000`. Veras la pantalla default de inicio , lo cual significa que tu proyecto esta funcionando. Cuando termines presiona Contol-C en la terminal para detener el servidor.

## Las partes de un proyecto ASP.NET Core
La plantilla `dotnet new mvc` genera un numero de archivos  y directorio para ti. Aqui estan las cosas mas importantes  que obtines fuera de la caja:

* Los archivos **Program.cs** and **Startup.cs** configuraran el servidor web  y la canalizacion de ASP.NET Core.La clase `Startup` es donde agregas el middleware que manipula ymodifica los solicitudes de entreatad y brindar cosas como archivos estaticos o paginas de errores. tambien en donde agrgas tus prpios servios al contenedor de deinyeccion de dependens \(more on this later\). 

* Los direcroruos **Models**, **Views**, and **Controllers** dcontiene componenetes de la architectura Modelo Vista COntrolador. Exproraras  los tres en el siguiente capiturlo.

* El directorio **wwwroot** cpntien assets como archivos estaticos como CSS, Javascriot e images. LOs crchivos en `wwwroot` seran despachados como contenido estatico y puden ser empaquetados y minificados  automaticamente.

* El archivo **appsettings.json** ficontiene los parametros de configuracion de que la aplicacion ASP.NET coRE cargar al inicio.Puedes alacenar aui tu las cadenas de coneccion o otras coas que no quieres que esten harcodeas den el codigo.

## Tips para Visual Studio Code

Si estas usando Visual Studio Code por primera vez ,aui tienes un par de tips de ayuda para iniciar:

* **Abrir el directorio raiz del proyecto**: En VIsual Studio Code, seleciona Archivo > Abrir carpeta. Si Visual Studio Code te solicita instalar los archivos pendeintes , presionar clic es Si para agregarlos.

* **F5 para ejecutar \(y puntos de interrupción de depuración\)**: Con tu proyecto abierto, presiona F5 pra ejecutar el proyecto en el modo de depuración, Esto es lo mismo que ejecutar `dotnet run` en la linea de comandos, pero tienes el beneficio de configurar puntos de interrupción en tu código dando doble clic en el margen izquierdo:

![Breakpoint in Visual Studio Code](breakpoint.png)

* **Foco para corregir problemas**: Si tu código contiene linea rojos \(errores del compilador , coloca el cursos sobre el codigo que esta en rojo y mirar el icono del foco encendido en el margen izquierdo. el foco te sugerirar reparaciones comunes, como agregar enunciados `using` faltantes en tu codigo:

![Lightbulb suggestions](lightbulb.png)

* **Compila rápidamente**: Usa el atajao `Command-Shift-B` o `Control-Shift-B` para ejecutar  la tarea de Build run la cual realiza lo mismo que `dotnet build`.

> Estos tips  tambien aplican para visual Studio \(not Code\) en Windows Si estas usandos Visual Studio, necesitaras abrie el archivo de proyecto directamente. Visual studio te solicitara guardar la el archivo de lasolucin, elcual debes guardar en el directorio raiz de la solucion\(la primera carpeta llamado `AspNetCoreTodo` \)- Tambiens puedes crear un proyecto ASP.NET Core directamenteo en  Visua Studio usando la plantitillas en Archivo - Nuevo Proyecto.

### Una nota acerca de Git

Si eres nuevo usando Git o Github para manejar el código fuente, ahora es buen momento para hacer un `git init` e inicializar el repositorio en el directorio raiz del proyecto:

```text
cd ..
git init
```

Asegúrate que agregues un archivo  `.gitignore` que ignora las carpeta `bin` and `obj` La plantilla de Visual Studio en Github .gitignore plantilla repositorio funciona genial.

Hay mucho más que explorar, así que profundicemos e iniciemos a construir una aplicación.
