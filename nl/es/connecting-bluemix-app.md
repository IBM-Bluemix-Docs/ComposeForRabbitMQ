---

copyright:
  years: 2016,2018
lastupdated: "2017-06-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Conexión de una aplicación {{site.data.keyword.cloud_notm}}

Para conectar una aplicación {{site.data.keyword.cloud}} al servicio, utilice las credenciales de servicio que se crean cuando se suministra el servicio. La app de ejemplo muestra cómo utilizar Node.js para conectar a un servicio de {{site.data.keyword.composeForRabbitMQ_full}} utilizando las credenciales y cómo crear una base de datos y leer y escribir en la base de datos.

## Conexión mediante la app de ejemplo 'Hello World'

La app de ejemplo [compose-rabbitmq-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-rabbitmq-helloworld-nodejs) muestra cómo utilizar Node.js para conectar a un servicio de {{site.data.keyword.composeForRabbitMQ}} utilizando las credenciales proporcionada. La aplicación crea, lee y escribe en una base de datos.

Descargue la app de ejemplo y siga las instrucciones del archivo readme. A continuación, en la página de detalles de la aplicación de {{site.data.keyword.cloud_notm}}, pulse **Ver APP** para ver el contenido de la tabla *ejemplos*.

## Credenciales disponibles

Nombre de campo|Descripción
----------|-----------
`uri`|El URI que se utilizará al conectarse al servicio. Incluye el esquema (`amqps:), el nombre de usuario y la contraseña del administrador, el nombre de host del servidor, el número de puerto al que se conecta y el nombre de vhost.
`uri_direct_1`|Un URI alternativo que se puede utilizar al conectarse al servicio. Formateado según `uri`.
`uri_admin`|Un URI que se debería visitar en un navegador para acceder a la interfaz de administración de la base de datos. El acceso requiere el nombre de usuario y la contraseña del administrador desde el campo `uri`.
`uri_admin_1`|Un URI de administración alternativo - consulte `uri_admin`.
`uri_admin_2`|Un URI de administración alternativo - consulte `uri_admin`.
`uri_admin_3`|Un URI de administración alternativo - consulte `uri_admin`.
`uri_admin_4`|Un URI de administración alternativo - consulte `uri_admin`.
`ca_certificate_base64`|Un certificado firmado automáticamente que se utiliza para confirmar que una aplicación se está conectando al servidor apropiado. Está codificado como base64. Debe decodificar la clave antes de utilizarla, como se muestra en la aplicación de ejemplo.
`deployment_id`|Un identificador interno para el servicio, tal como se ha creado en Compose.
`db_type`|El tipo de base de datos que ofrece el servicio; en este caso, `rabbitmq`.
`name`|El nombre del despliegue de la base de datos.
{: caption="Tabla 1. Credenciales de Compose for RabbitMQ" caption-side="top"}
