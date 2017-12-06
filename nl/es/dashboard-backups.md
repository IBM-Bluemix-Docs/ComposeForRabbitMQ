---

copyright:
  years: 2017
lastupdated: "2017-10-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Copias de seguridad
{: #backups}

Puede crear y descargar copias de seguridad desde la página *Gestionar* del panel de control del servicio. Dispone de copias de seguridad planificadas y manuales.

## Visualización de las copias de seguridad existentes

Se planifican automáticamente copias de seguridad diarias de la base de datos. Para ver las copias de seguridad existentes, vaya a la página *Gestionar* del panel de control del servicio. 

![Copias de seguridad](./images/rabbitmq-backups-show.png "Una lista de copias de seguridad del panel de control del servicio")

Pulse en la fila correspondiente para ampliar las opciones para cualquier copia de seguridad disponible.

![Opciones de copia de seguridad](./images/rabbitmq-backups-options.png "Opciones de una copia de seguridad.") 

## Creación de una copia de seguridad a petición

Además de copias de seguridad planificadas, puede crear una copia de seguridad manualmente. Para crear una copia de seguridad manual, vaya a la página *Gestionar* del panel de control del servicio y pulse *Copia de seguridad ahora*.

## Descarga de una copia de seguridad

Para descargar una copia de seguridad, vaya a la página *Gestionar* del panel de control del servicio y pulse *Descargar* en la fila correspondiente a la copia de seguridad que desea descargar.

## Contenido de una copia de seguridad

Las copias de seguridad de RabbitMQ son una representación JSON de los metadatos del intermediario. Se crean a partir de un mandato de exportación proporcionada por el plugin de gestión de RabbitMQ. La ejecución de la exportación en el servicio no afecta al rendimiento.

## Utilización de una copia de seguridad con una base de datos local

Puede utilizar la copia de seguridad de {{site.data.keyword.composeForRabbitMQ}} para ejecutar una copia local de la base de datos.

Deberá tener una instancia local de RabbitMQ en ejecución, con el plugin de gestión incluido en la distribución de RabbitMQ. Habilítelo con `rabbitmq-plugins enable rabbitmq_management`. Con esto obtendrá adicionalmente:

* la IU de administración en `http://localhost:15672/`,
* la API HTTP en `http://server-name:15672/api/`,
* y la herramienta de línea de mandatos de la API `rabbitmqadmin` en `http://localhost:15672/cli/ `.

Para importar el archivo de copia de seguridad JSON, puede hacer lo siguiente:

* Con la IU de administración de http://localhost:15672/, utilice la función _Importar/Exportar definiciones_ de la parte inferior de la página _Visión general_.
* Mediante la API, envíe un POST a `http://server-name:15672/api/definitions`, por ejemplo:
```http
curl -i -u guest:guest -H "content-type:application/json" -X POST --data @<path_to_your_rabbitmq_backup> http://localhost:15672/api/definitions
```
* utilice el mandato `rabbitmqadmin import <your_rabbitmq_backup>`.

## Restauración de una copia de seguridad

Para restaurar una copia de seguridad en una nueva instancia de servicio, siga los pasos para ver las copias de seguridad existentes y luego pulse en la fila correspondiente para ampliar las opciones para la copia de seguridad que desea descargar. Pulse el botón **Restaurar**. Se mostrará un mensaje que le indicará que se ha iniciado una restauración. A la nueva instancia del servicio se le asignará automáticamente el nombre "rabbitmq-restore-[timestamp]" y aparecerá en el panel de control cuando comience el suministro.
