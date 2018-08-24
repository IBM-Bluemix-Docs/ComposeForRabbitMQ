---

copyright:
  years: 2016,2018
lastupdated: "2018-01-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Tarifas
{: #pricing}

## Configuración básica
Un servicio de {{site.data.keyword.composeForRabbitMQ_full}} se inicia como un clúster con dos nodos de datos, y cada uno de los cuales contiene 256 MB de memoria y 256 MB de almacenamiento, lo que es igual a 1 unidad de recursos. El servicio _incluye_ la réplica y la alta disponibilidad, de modo que cada unidad y el precio por unidad _incluye_ el coste de los recursos en los dos nodos.

La configuración básica también incluye dos portales de HAProxy. Cada uno de los dos portales de HAProxy tiene 64 MB de memoria.

La configuración de servicio base tiene un precio establecido. Consulte los mosaicos del catálogo en {{site.data.keyword.cloud_notm}} para ver los precios básicos en su moneda local. Por ejemplo, el precio básico en dólares de EE.UU. es de 19,50 dólares/mes.

## Incremento de recursos
Si necesita más almacenamiento o memoria para el servicio, puede aumentar los recursos asignados en una proporción 1:1 de almacenamiento de disco para la unidad de memoria. Aumentar el disco asignado al despliegue también aumentará la RAM asignada. Una unidad de {{site.data.keyword.composeForRabbitMQ}} consta de 256 MB de almacenamiento y de 256 MB de memoria, y cada unidad y el precio por unidad _incluye_ el coste para aumentar los recursos en ambos nodos.

## Cálculo del coste del despliegue
{: #tiered-pricing}

Cada unidad adicional (256 MB de almacenamiento/256 MB de memoria) tiene un precio por unidad, que aparece en la moneda local en el mosaico del catálogo de {{site.data.keyword.cloud_notm}} para el servicio. En dólares de EE.UU., cada unidad adicional cuesta 19,50 dólares. A medida que aumenta el tamaño _total_ de todos los servicios de {{site.data.keyword.composeForRabbitMQ}}, el precio por unidad disminuye, tal como se muestra en la tabla de precios por niveles, más abajo.

Número de unidades|Precio por unidad
----------|-----------
De 1 a 9 unidades|precio básico por unidad -- 19,50 dólares USD/unidad
De 10 a 24 unidades|10 % de reducción -- 17,55 dólares USD/unidad
De 25 a 49 unidades|20 % de reducción -- 15,60 dólares USD/unidad
De 50 a 99 unidades|30 % de reducción -- 13,65 dólares USD/unidad
De 100 a 499 unidades|40 % de reducción -- 11,70 dólares USD/unidad
De 500 a 999 unidades|50 % de reducción -- 9,75 dólares USD/unidad
De 1.000 a 4.999 unidades|60 % de reducción -- 7,80 dólares USD/unidad
Más de 5.000 unidades|70 % de reducción -- 5,85 dólares USD/unidad
{: caption="Tabla 1. Precio por niveles de {{site.data.keyword.composeForRabbitMQ}}" caption-side="top"}

