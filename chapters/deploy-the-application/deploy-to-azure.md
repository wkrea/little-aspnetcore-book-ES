## Desplegar en Azure

La implementación de la aplicación de ASP.NET Core en Azure solo lleva unos pocos pasos. Puede hacerlo a través del portal web de Azure o en la línea de comandos utilizando la CLI de Azure. Voy a cubrir este último.

### Lo que necesitarás

* Git (usa `git --version` para asegurarte de que esté instalado)
* El CLI de Azure (siga las instrucciones de instalación en https://github.com/Azure/azure-cli)
* Una suscripción de Azure (la suscripción gratuita está bien)
* Un archivo de configuración de implementación en la raíz de su proyecto.

### Crear un archivo de configuración de implementación

Como hay múltiples proyectos en la estructura de su directorio (la aplicación web y dos proyectos de prueba), Azure no sabrá cuál publicar. Para solucionar este problema, cree un archivo llamado `.deployment` en la parte superior de la estructura de su directorio:

**.deployment**

```ini
[config]
project = AspNetCoreTodo/AspNetCoreTodo.csproj
```

Asegúrese de guardar el archivo como `.deployment` sin otras partes en el nombre. (En Windows, puede que tenga que poner comillas alrededor del nombre del archivo, como `".deployment"`, para evitar que se agregue una extensión `.txt`.)

Si ejecuta el comando `ls` o `dir` en su directorio principal del proyecto, debería ver estos elementos:

```
.deployment
AspNetCoreTodo
AspNetCoreTodo.IntegrationTests
AspNetCoreTodo.UnitTests
```

### Configurar los recursos de Azure

Si acaba de instalar la CLI de Azure por primera vez, ejecute


```
az login
```

y siga las instrucciones para iniciar sesión en su máquina. Luego, crea un nuevo grupo de recursos para esta aplicación:

```
az group create -l westus -n AspNetCoreTodoGroup
```

Esto crea un grupo de recursos en la región oeste de los Estados Unidos. Si está ubicado lejos del oeste de los EE. UU., Use `az account list-locations` para obtener una lista de ubicaciones y encontrar una más cercana a usted.

A continuación, cree un plan de Servicio de aplicaciones en el grupo que acaba de crear:

```
az appservice plan create -g AspNetCoreTodoGroup -n AspNetCoreTodoPlan --sku F1
```

> F1 es el plan de aplicación gratuita. Si desea usar un nombre de dominio personalizado con su aplicación, use el plan D1 ($ 10 / mes) o superior.

Ahora cree una aplicación web en el plan del servicio de aplicaciones:

```
az webapp create -g AspNetCoreTodoGroup -p AspNetCoreTodoPlan -n MyTodoApp
```

El nombre de la aplicación (`MyTodoApp` arriba) debe ser globalmente único en Azure. Una vez que se crea la aplicación, tendrá una URL predeterminada en el formato: http://mytodoapp.azurewebsites.net


### Implementa tus archivos de proyecto en Azure

Puede usar Git para enviar sus archivos de aplicación a la aplicación web de Azure. Si su directorio local no ha sido rastreado como un repositorio de Git, ejecute estos comandos para configurarlo:
```
git init
git add .
git commit -m "First commit!"
```

A continuación, cree un nombre de usuario y contraseña de Azure para la implementación:

```
az webapp deployment user set --user-name nate
```

Siga las instrucciones para crear una contraseña. Luego usa `config-local-git` para generar una URL de Git:

```
az webapp deployment source config-local-git -g AspNetCoreTodoGroup -n MyTodoApp --out tsv

https://nate@mytodoapp.scm.azurewebsites.net/MyTodoApp.git
```

Copie la URL en el portapapeles y utilícela para agregar un control remoto Git a su repositorio local:

```
git remoto add azure <paste>
```

Solo necesitas hacer estos pasos una vez. Ahora, cuando quiera enviar sus archivos de aplicaciones a Azure, verifíquelos con Git y ejecute

```
git push azure master
```

Ver una secuencia de mensajes de registro a medida que la aplicación se implementa en Azure.

Cuando esté completo, vaya a http://yourappname.azurewebsites.net para ver la aplicación.