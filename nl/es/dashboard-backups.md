---

copyright:
  years: 2017,2018
lastupdated: "2018-03-02"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Copias de seguridad
{: #backups}

Puede crear y descargar copias de seguridad desde el separador _Copias de seguridad_ de la página _Gestionar_ del panel de control del servicio. Las copias de seguridad diarias, semanales, mensuales y bajo demanda están disponibles. Se retienen según la siguiente planificación:

Tipo de copia de seguridad|Planificación de retención
----------|-----------
Diario|Las copias de seguridad diarias se retienen durante 7 días
Semanal|Las copias de seguridad semanales se retienen durante 4 semanas
Mensual|Las copias de seguridad mensuales se retienen durante 3 meses
Bajo demanda|Se retiene una copia de seguridad bajo demanda. La copia de seguridad retenida es siempre la copia de seguridad bajo demanda más reciente.
{: caption="Tabla 1. Planificación de retención de copia de seguridad" caption-side="top"}

Las planificaciones de copia de seguridad y las políticas de retención están corregidas. Si necesita mantener más copias de seguridad de las que permite la planificación de retención, debe descargar copias de seguridad y retener archivados según sus necesidades empresariales.

## Visualización de las copias de seguridad existentes

Se planifican automáticamente copias de seguridad diarias de la base de datos. Para ver las copias de seguridad existentes, vaya a la página *Gestionar* del panel de control del servicio. 

  ![Copias de seguridad](./images/rabbitmq-backups-show.png "Una lista de copias de seguridad del panel de control del servicio")

Pulse en la fila correspondiente para ampliar las opciones para cualquier copia de seguridad disponible.

  ![Opciones de copia de seguridad](./images/rabbitmq-backups-options.png "Opciones de una copia de seguridad.") 

### Utilización de la API para ver las copias de seguridad existentes

Hay una lista de copias de seguridad disponible en el punto final `GET /2016-07/deployments/:id/backups`. El punto final de la fundación con el ID de la instancia de servicio y el ID de despliegue se muestran en la _Visión general_ del servicio. Por ejemplo: 
``` 
https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
```  

## Creación de una copia de seguridad a petición

Además de copias de seguridad planificadas, puede crear una copia de seguridad manualmente. Para crear una copia de seguridad manual, vaya a la página *Gestionar* del panel de control del servicio y pulse *Copia de seguridad ahora*.

### Utilización de la API para crear una copia de seguridad

Envíe una solicitud POST al punto final de las copias de seguridad para iniciar una copia de seguridad manual: `POST /2016-07/deployments/:id/backups`. Se devuelve inmediatamente con el ID de receta e información sobre la copia de seguridad mientras está en ejecución. Es necesario comprobar el punto final de copias de seguridad para ver si la copia de seguridad ha finalizado y buscar su backup_id antes de utilizarla. Utilice `GET /2016-07/deployments/:id/backups/`.

## Descarga de una copia de seguridad

Para descargar una copia de seguridad, vaya a la página *Gestionar* del panel de control del servicio y pulse *Descargar* en la fila correspondiente a la copia de seguridad que desea descargar.

### Utilización de la API para descargar una copia de seguridad

Busque la copia de seguridad que desea restaurar en la página _Copias de seguridad_ del servicio y copie el backup_id, o utilice `GET /2016-07/deployments/:id/backups` para buscar una copia de seguridad y su backup_id mediante la API de Compose. A continuación, utilice el backup_id para buscar información y un enlace de descarga de una copia de seguridad específica: `GET /2016-07/deployments/:id/backups/:backup_id`.

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

### Restauración mediante la CLI de {{site.data.keyword.cloud_notm}}

Utilice los pasos siguientes para restaurar una copia de seguridad de un servicio RabbitMQ en ejecución a un servicio RabbitMQ nuevo mediante la CLI de {{site.data.keyword.cloud_notm}}. 
1. Si es necesario, [descárguela e instálela](https://console.{DomainName}/docs/cli/index.html#overview). 
2. Busque la copia de seguridad que desea restaurar en la página _Copias de seguridad_ del servicio y copie el ID de copia de seguridad.  
  **O**  
  Utilice `GET /2016-07/deployments/:id/backups` para buscar una copia de seguridad y su ID mediante la API de Compose. El punto final de la fundación y el ID de la instancia de servicio se muestran en la _Visión general_ del servicio. Por ejemplo: 
  ``` 
  https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
  ```  
  La respuesta incluirá una lista de todas las copias de seguridad disponibles para dicha instancia de servicio. Seleccione la copia de seguridad desde la que desea restaurar y copie su ID.

3. Inicie sesión con la cuenta y las credenciales adecuadas. `ibmcloud login` (o `ibmcloud login -help` para ver todas las opciones de inicio).

4. Vaya a su organización y espacio `ibmcloud target -o "$YOUR_ORG" -s "YOUR_SPACE"`

5. Utilice el mandato `service create` para suministrar un nuevo servicio y proporcione el servicio de origen y la copia de seguridad específica que va a restaurar en un objeto JSON. Por ejemplo:
``` 
ibmcloud service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": "$BACKUP_ID" }'
```
  El campo _SERVICE_ debe ser compose-for-rabbitmq, y el campo _PLAN_ debe ser Standard o Enterprise, según el entorno. _SERVICE\_INSTANCE\_NAME_ es donde se pondrá el nombre del nuevo servicio. _source\_service\_instance\_id_ es el ID de instancia de servicio del origen de la copia de seguridad; puede obtenerse ejecutando `ibmcloud cf service DISPLAY_NAME --guid`, donde _DISPLAY\_NAME_ es el nombre del servicio RabbitMQ del que es la copia de seguridad. 
  
  
### Migración a una nueva versión

Algunas actualizaciones de versión principal no están disponibles en el despliegue en ejecución actual. Deberá suministrar un nuevo servicio que esté ejecutando la versión actualizada y luego migrar allí los datos mediante una copia de seguridad. Este proceso es el mismo que el anterior de restaurar una copia de seguridad, excepto que se especifica la versión a la que desea actualizar.

``` 
ibmcloud service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": ""$BACKUP_ID", "db_version":"$VERSION_NUMBER" }'
```

Por ejemplo, la restauración de una versión anterior de un servicio {{site.data.keyword.composeForRabbitMQ}} a un servicio nuevo que ejecute RabbitMQ 3.6.14 tendrá este aspecto:
```
ibmcloud service create compose-for-rabbitmq Standard migrated_rabbit -c '{ "source_service_instance_id": "0269e284-dcac-4618-89a7-f79e3f1cea6a", "backup_id":"5a96d8a7e16c090018884566", "db_version":"3.6.14"  }'

