# Usar una base de datos

Escribir código de acceso a base de datos puede ser complicado. Es una mala idea pegar consultas de SQL en el código de su aplicación, a menos que realmente sepa lo que está haciendo. Un **mapeador de objetos relacional** (ORM) facilita la escritura de código que interactúa con una base de datos agregando una capa de abstracción entre su código y la base de datos en sí. Hibernate para Java y ActiveRecord para Ruby son dos ORM bien conocidos.

Existen varios ORM para .NET, incluido uno creado por Microsoft e incluido en ASP.NET Core de forma predeterminada: Entity Framework Core. Entity Framework Core facilita la conexión a varios tipos de bases de datos diferentes y le permite utilizar el código C# para crear consultas de base de datos que se asignan nuevamente a los modelos C# (POCO Plain Old CLR Objects).

> ¿Recuerda cómo crear una interfaz de servicio desacopla el código del controlador de la clase de servicio real? Entity Framework Core es como una gran interfaz sobre su base de datos. Su código de C# puede permanecer independiente de la base de datos, y puede intercambiar diferentes proveedores dependiendo de la tecnología de base de datos subyacente.

Entity Framework Core puede conectarse a bases de datos relacionales como SQL Server, PostgreSQL y MySQL también funciona con bases de datos NoSQL (documentos) como Mongo. Durante el desarrollo, usarás SQLite en este proyecto para facilitar la configuración
