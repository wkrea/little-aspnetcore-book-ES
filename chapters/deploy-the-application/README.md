# Desplegar la aplicación
Has recorrido un largo camino, pero aún no has terminado. Una vez que has creado una gran aplicación, ¡debes compartirla con el mundo!

Debido a que las aplicaciones ASP.NET Core pueden ejecutarse en Windows, Mac o Linux, existen varias formas diferentes de implementar su aplicación. En este capítulo, te mostraré las formas más comunes (y más fáciles) de estar en línea.

## Opciones de implementación

Las aplicaciones de ASP.NET Core se implementan normalmente en uno de estos entornos:

* **Un host Docker**. Cualquier máquina capaz de albergar contenedores Docker puede usarse para alojar una aplicación ASP.NET Core. Crear una imagen de Docker es una forma muy rápida de implementar su aplicación, especialmente si está familiarizado con Docker. (Si no lo eres, ¡no te preocupes! Cubriré los pasos más adelante).

* **Azure**. Microsoft Azure tiene soporte nativo para las aplicaciones de ASP.NET Core. Si tiene una suscripción a Azure, solo necesita crear una aplicación web y cargar los archivos de su proyecto. Cubriré cómo hacer esto con el CLI de Azure en la siguiente sección.

* **Linux (con Nginx)**. Si no desea ir a la ruta Docker, aún puede alojar su aplicación en cualquier servidor Linux (esto incluye las máquinas virtuales Amazon EC2 y DigitalOcean). Es típico emparejar ASP.NET Core con el proxy inverso Nginx. (Más sobre Nginx a continuación.)


* **Windows**. Puede usar el servidor web IIS en Windows para alojar aplicaciones ASP.NET Core. Por lo general, es más fácil (y más barato) implementarlo en Azure, pero si prefiere administrar los servidores de Windows usted mismo, funcionará bien.

## Kestrel y proxies inversos

> Si no le importan las agallas de hospedar aplicaciones ASP.NET Core y solo desea las instrucciones paso a paso, siéntase libre de saltar a una de las siguientes dos secciones.

ASP.NET Core incluye un servidor web rápido y ligero llamado Kestrel. Es el servidor que ha estado usando cada vez que ejecutó `dotnet run` y navegó a `http://localhost:5000`. Cuando implementas tu aplicación en un entorno de producción, aún utilizará Kestrel detrás de escena. Sin embargo, se recomienda que coloque un proxy inverso delante de Kestrel, porque Kestrel aún no tiene equilibrio de carga y otras características que tienen los servidores web más maduros.

En Linux (y en los contenedores Docker), puede usar Nginx o el servidor web Apache para recibir solicitudes entrantes de Internet y enrutarlas a su aplicación alojada con Kestrel. Si estás en Windows, IIS hace lo mismo.

Si está utilizando Azure para alojar su aplicación, todo esto se hace automáticamente. Cubriré la configuración de Nginx como un proxy inverso en la sección de Docker.