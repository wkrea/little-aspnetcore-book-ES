## Desplegando con Docker

Si no está utilizando una plataforma como Azure, las tecnologías de contenedorización como Docker pueden facilitar la implementación de aplicaciones web en sus propios servidores. En lugar de dedicar tiempo a configurar un servidor con las dependencias que necesita para ejecutar su aplicación, copiar archivos y reiniciar procesos, simplemente puede crear una imagen de Docker que describa todo lo que su aplicación necesita para ejecutar y girarla como un contenedor en cualquier Docker. anfitrión.

Docker también puede hacer que escalar su aplicación en múltiples servidores sea más fácil. Una vez que tenga una imagen, usarla para crear 1 contenedor es el mismo proceso que crear 100 contenedores.

Antes de comenzar, necesita tener instalada la CLI de Docker en su máquina de desarrollo. Busque "get docker for (mac / windows / linux)" y siga las instrucciones en el sitio web oficial de Docker. Puedes verificar que está instalado correctamente con

```
docker version
```

### Añadir un archivo docker

Lo primero que necesitará es un Dockerfile, que es como una receta que le dice a Docker lo que su aplicación necesita para construir y ejecutar.

Cree un archivo llamado `Dockerfile` (sin extensión) en la carpeta raíz, nivel superior `AspNetCoreTodo`. Ábrelo en tu editor favorito. Escribe la siguiente línea:

```dockerfile
FROM microsoft/dotnet:2.0-sdk AS build
```

Esto le dice a Docker que use la imagen `microsoft/dotnet:2.0-sdk` como punto de partida. Esta imagen es publicada por Microsoft y contiene las herramientas y dependencias que necesita para ejecutar `dotnet build` y compilar su aplicación. Al utilizar esta imagen preconstruida como punto de partida, Docker puede optimizar la imagen producida para su aplicación y mantenerla pequeña.

A continuación, agregue esta línea:

```dockerfile
COPY AspNetCoreTodo/*.csproj ./app/AspNetCoreTodo/
```

El comando `COPY` copia el archivo de proyecto `.csproj` en la imagen en la ruta `/app/AspNetCoreTodo/`. Tenga en cuenta que todavía no se ha copiado en la imagen ninguno del código real (archivos `.cs`). Verás por qué en un minuto.

```dockerfile
WORKDIR /app/AspNetCoreTodo
RUN dotnet restore
```

`WORKDIR` es el equivalente de Docker de` cd`. Esto significa que cualquier comando ejecutado a continuación se ejecutará desde el directorio `/ app / AspNetCoreTodo` que el comando` COPY` creó en el último paso.

Ejecutar el comando `dotnet restore` restaura los paquetes NuGet que necesita la aplicación, definidos en el archivo `.csproj`. Al restaurar los paquetes dentro de la imagen **antes** agregando el resto del código, Docker puede almacenar en caché los paquetes restaurados. Luego, si realiza cambios de código (pero no cambia los paquetes definidos en el archivo de proyecto), la reconstrucción de la imagen de Docker será súper rápida.

Ahora es el momento de copiar el resto del código y compilar la aplicación:

```dockerfile
COPY AspNetCoreTodo/. ./AspNetCoreTodo/
RUN dotnet publish -o out /p:PublishWithAspNetCoreTargetManifest="false"
```

El comando `dotnet publish` compila el proyecto, y el indicador` -o out` coloca los archivos compilados en un directorio llamado `out`.

Estos archivos compilados se utilizarán para ejecutar la aplicación con los últimos comandos:

```dockerfile
FROM microsoft/dotnet:2.0-runtime AS runtime
ENV ASPNETCORE_URLS http://+:80
WORKDIR /app
COPY --from=build /app/AspNetCoreTodo/out ./
ENTRYPOINT ["dotnet", "AspNetCoreTodo.dll"]
```

El comando `FROM` se usa nuevamente para seleccionar una imagen más pequeña que solo tiene las dependencias necesarias para ejecutar la aplicación. El comando `ENV` se usa para establecer variables de entorno en el contenedor, y la variable de entorno` ASPNETCORE_URLS` le dice a ASP.NET Core a qué interfaz de red y puerto debe enlazarse (en este caso, el puerto 80).

El comando `ENTRYPOINT` permite a Docker saber que el contenedor debe iniciarse como un ejecutable ejecutando` dotnet AspNetCoreTodo.dll`. Esto le dice a `dotnet` que inicie su aplicación desde el archivo compilado creado por` dotnet publish` anteriormente. (Cuando haces `dotnet run` durante el desarrollo, estás logrando lo mismo en un solo paso).

El Dockerfile completo se ve así:

**Dockerfile**

```dockerfile
FROM microsoft/dotnet:2.0-sdk AS build
COPY AspNetCoreTodo/*.csproj ./app/AspNetCoreTodo/
WORKDIR /app/AspNetCoreTodo
RUN dotnet restore

COPY AspNetCoreTodo/. ./
RUN dotnet publish -o out /p:PublishWithAspNetCoreTargetManifest="false"

FROM microsoft/dotnet:2.0-runtime AS runtime
ENV ASPNETCORE_URLS http://+:80
WORKDIR /app
COPY --from=build /app/AspNetCoreTodo/out ./
ENTRYPOINT ["dotnet", "AspNetCoreTodo.dll"]
```

### Crear una imagen

Asegúrese de que Dockerfile esté guardado y luego use `docker build` para crear una imagen:

```
docker build -t aspnetcoretodo .
```

¡No te pierdas el período final! Eso le dice a Docker que busque un Dockerfile en el directorio actual.

Una vez creada la imagen, puede ejecutar `docker images` para listar todas las imágenes disponibles en su máquina local. Para probarlo en un contenedor, ejecute

```
docker run --name aspnetcoretodo_sample --rm -it -p 8080:80 aspnetcoretodo
```

El indicador `-it` le dice a Docker que ejecute el contenedor en modo interactivo (dando salida al terminal, en lugar de ejecutarse en segundo plano). Cuando quieras detener el contenedor, presiona Control-C.

¿Recuerda la variable `ASPNETCORE_URLS` que le dijo a ASP.NET Core que escuche en el puerto 80? La opción `-p 8080: 80` le dice a Docker que asigne el puerto 8080 en *su* máquina al *puerto* del contenedor 80. Abra su navegador y navegue a http://localhost:8080 para ver la aplicación que se ejecuta en el contenedor !

### Configurar Nginx

Al principio de este capítulo, mencioné que debería usar un proxy inverso como Nginx para enviar las solicitudes a Kestrel. También puedes usar Docker para esto.

La arquitectura general constará de dos contenedores: un contenedor Nginx que escucha en el puerto 80 y reenvía las solicitudes al contenedor que acaba de construir y aloja su aplicación con Kestrel.

El contenedor Nginx necesita su propio archivo Docker. Para evitar que entre en conflicto con el Dockerfile que acaba de crear, cree un nuevo directorio en la raíz de la aplicación web:

```
mkdir nginx
```

Crea un nuevo Dockerfile y agrega estas líneas

**nginx/Dockerfile**

```dockerfile
FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
```

Next, create an `nginx.conf` file:

**nginx/nginx.conf**

```
events { worker_connections 1024; }

http {
    server {
        listen 80;
        location / {
          proxy_pass http://kestrel:80;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection 'keep-alive';
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
    }
}
```

Este archivo de configuración le dice a Nginx que haga proxy de las solicitudes entrantes a `http://kestrel:80`. (Verás por qué `kestrel` funciona como nombre de host en un momento).

> Cuando implementa su aplicación en un entorno de producción, debe agregar la directiva `server_name` y validar y restringir el encabezado del host a valores buenos conocidos. Para más información, ver:

> https://github.com/aspnet/Announcements/issues/295

### Set up Docker Compose

Aquí hay un archivo más para crear. Arriba en el directorio raíz, crea `docker-compose.yml`:

**docker-compose.yml**

```yaml
nginx:
    build: ./nginx
    links:
        - kestrel:kestrel
    ports:
        - "80:80"
kestrel:
    build: .
    ports:
        - "80"
```

Docker Compose es una herramienta que te ayuda a crear y ejecutar aplicaciones de múltiples contenedores. Este archivo de configuración define dos contenedores: `nginx` de la receta `./Nginx/Dockerfile`, y `kestrel` de la receta `./Dockerfile`. Los contenedores están explícitamente vinculados entre sí para que puedan comunicarse.

Puede intentar girar toda la aplicación multi-contenedor ejecutando:

```
docker-compose up
```

Intente abrir un navegador y navegue a http://localhost (puerto 80, no 8080). Nginx escucha en el puerto 80 (el puerto HTTP predeterminado) y envía solicitudes a su aplicación ASP.NET Core alojada por Kestrel.

### Configurar un servidor Docker

Las instrucciones de configuración específicas están fuera del alcance de este libro, pero se puede usar cualquier versión moderna de Linux (como Ubuntu) para configurar un host Docker. Por ejemplo, podría crear una máquina virtual con Amazon EC2 e instalar el servicio Docker. Puede buscar "amazon ec2 setup up docker" (por ejemplo) para obtener instrucciones.

Me gusta usar DigitalOcean porque han hecho que sea muy fácil comenzar. DigitalOcean tiene una máquina virtual Docker pre-construida y tutoriales detallados para que Docker esté en funcionamiento (busque "docker digitalocean").
