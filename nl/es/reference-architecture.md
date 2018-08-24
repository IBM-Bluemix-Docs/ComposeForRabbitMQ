---
copyright:
  years: 2016,2018
lastupdated: "2018-06-13"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Arquitectura 
{: #architecture}

## Réplica

Un servicio de {{site.data.keyword.composeForRabbitMQ_full}} contiene tres nodos que actúan de forma unánime como un intermediario altamente disponible, cada uno ejecutando la aplicación RabbitMQ y compartiendo usuarios, hosts virtuales, colas, intercambios, enlaces y parámetros de tiempo de ejecución. Cuando se selecciona una región en la que se despliega el servicio, los tres nodos se dispersan en las zonas de disponibilidad de la región, y todos los datos y estados necesarios para la operación de un intermediario de RabbitMQ se replican en todos los nodos. 

Un nodo funciona como un maestro y los otros funcionan como duplicaciones. Las duplicaciones aplican las operaciones que se producen en el maestro en exactamente el mismo orden que el maestro y, por lo tanto, mantienen el mismo estado. Todas las acciones que no sean una acción de publicación van solo al maestro, y el maestro difunde entonces el efecto de las acciones a las duplicaciones. Si el maestro falla, se promociona una de las duplicaciones y el intermediario de RabbitMQ funciona con normalidad.

## Cifrado de disco

Todos los servicios de {{site.data.keyword.composeForRabbitMQ}} tienen cifrado en reposo. Los datos residen en servidores que tienen el cifrado de nivel de volumen habilitado. 

Puesto que {{site.data.keyword.composeForRabbitMQ}} es un servicio gestionado, es posible que los operadores de {{site.data.keyword.cloud}} Compose vean los datos. Además del cifrado de disco, le recomendamos que, si está pasando información personal a través de la cola de mensajería, cifre la información antes de enviarla o utilizando extensiones o características para habilitar el cifrado en el propio RabbitMQ. Si bien estos métodos pueden afectar a la usabilidad o al rendimiento, es una buena práctica asegurarse de que la información personal esté protegida con cifrado.


## Escalado automático

Los recursos se escalan automáticamente basándose en el uso de memoria total del intermediario de RabbitMQ. El almacenamiento se basa en una proporción de RAM suministrada de 1:1. Estos recursos se empaquetan como unidades.

El escalado automático está diseñado para responder a las tendencias a corto y medio plazo de la base de datos. Cada minuto, el servicio está marcado y si se está quedando corto en RAM, se asignan más unidades al despliegue. 

El escalado automático no reduce los despliegues en los que el uso de disco/memoria se ha reducido. Los recursos suministrados a las bases de datos permanecen para sus necesidades futuras, o hasta que reduzca el despliegue manualmente.
{: .tip}
