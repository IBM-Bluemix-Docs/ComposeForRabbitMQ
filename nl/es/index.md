---

copyright:
  years: 2016,2018
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Acerca de {{site.data.keyword.composeForRabbitMQ}}
{: #about-compose-for-rabbitmq}

RabbitMQ maneja los mensajes de forma asíncrona entre las aplicaciones y las bases de datos, permitiendo la separación de los datos y de las capas de aplicación. RabbitMQ permite a los desarrolladores direccionar, realizar un seguimiento y poner en cola mensajes con niveles de persistencia personalizables, valores de entrega y publicación confirmada. Al utilizar {{site.data.keyword.composeForRabbitMQ_full}}, obtendrá acceso a la interfaz administrativa fácil de utilizar con un host de características de gestión como por ejemplo la supervisión de despliegue, el escalado de pulsación de un botón, la configuración de usuarios y el acceso de archivos de registro.
{:shortdesc}

**Nota:** Cualquier instancia de servicio de Compose proporcionada antes del 14 de septiembre de 2016 que siga activa se puede seguir utilizando y se puede acceder a ella directamente en [https://www.compose.com/](https://www.compose.com). Se puede acceder directamente a cualquier instancia de servicio de Compose que se proporcione desde este momento en adelante y se utilizará dentro de la cuenta de {{site.data.keyword.cloud}}.

## Creación de una instancia de servicio de {{site.data.keyword.composeForRabbitMQ}}

Puede crear un servicio de {{site.data.keyword.composeForRabbitMQ}} desde la [página de {{site.data.keyword.composeForRabbitMQ}}](https://console.{DomainName}/catalog/services/compose-for-rabbitmq/) en el catálogo de {{site.data.keyword.cloud_notm}}.

Elija un nombre de servicio, y una región, organización y espacio en los que suministrar el servicio. Puede utilizar el campo **Seleccionar una versión de base de datos** para elegir entre las versiones de bases de datos disponibles.

Cuando suministre la instancia de {{site.data.keyword.composeForRabbitMQ}}, puede elegir los planes *Estándar* o *Empresa*. Con el plan *Empresa*, puede suministrar la instancia de {{site.data.keyword.composeForRabbitMQ}} en un clúster disponible de {{site.data.keyword.composeEnterprise}}. {{site.data.keyword.composeEnterprise}} proporciona la seguridad y nivel de aislamiento necesarios para el cumplimiento de las reglas empresariales y utiliza redes dedicadas para garantizar el rendimiento de las bases de datos desplegadas. Consulte la [documentación de Compose Enterprise](../ComposeEnterprise/index.html) para ver más detalles.

## Gestión de {{site.data.keyword.composeForRabbitMQ}}

Puede gestionar el servicio desde el panel de control del servicio. Aquí encontrará información sobre su base de datos de {{site.data.keyword.cloud_notm}} Compose y cómo conectarse a la misma. También puede:
- gestionar copias de seguridad 
- asignar más recursos para el servicio 
- cambiar la contraseña del servicio
- utilizar listas blancas para restringir el acceso a las bases de datos. 
Para obtener más información, consulte [Valores](./dashboard-settings.html).

## Conexión a {{site.data.keyword.composeForRabbitMQ}}

Puede conectarse a su servicio utilizando las credenciales que se crean junto con el servicio, o con las series de conexión y la línea de mandatos que se proporcionan en el separador *Visión general* del panel de control del servicio.

## Conexión de una aplicación {{site.data.keyword.cloud_notm}} a {{site.data.keyword.composeForRabbitMQ}}

Para conectar una aplicación {{site.data.keyword.cloud_notm}} al servicio, utilice las credenciales que se crean junto con el servicio. Encontrará información sobre cómo conectar una aplicación {{site.data.keyword.cloud_notm}} a un servicio {{site.data.keyword.composeForRabbitMQ}} en el apartado [Conexión de una aplicación {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Conexión a {{site.data.keyword.composeForRabbitMQ}} desde fuera de {{site.data.keyword.cloud_notm}}

Si desea conectarse a {{site.data.keyword.composeForRabbitMQ}} desde fuera de {{site.data.keyword.cloud_notm}}, puede utilizar las series de conexión proporcionadas o la línea de mandatos. Encontrará información sobre cómo conectar en el apartado [Conexión de una aplicación externa](./connecting-external.html).
