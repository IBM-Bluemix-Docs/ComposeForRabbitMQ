---

copyright:
  years: 2016,2017
lastupdated: "2017-04-27"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Données d'identification disponibles
{: #available-credentials}

Pour connecter une application à votre service, utilisez les données d'identification créées en même temps que le service. 
Le modèle d'application [compose-rabbitmq-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-rabbitmq-helloworld-nodejs) montre comment utiliser Node.js pour établir une connexion au service
{{site.data.keyword.composeForRabbitMQ}} à l'aide des données d'identification.

Nom de zone|Description
----------|-----------
`uri`|URI à utiliser pour établir la connexion au service. Comprend le schéma (`amqps:), le nom et le mot de passe de l'administrateur, le nom d'hôte du serveur, le numéro de port auquel se connecter et le nom vhost.
`uri_direct_1`|URI alternatif qui peut être utilisé lors de la connexion au service. Utilise le même format qu'`uri`.
`uri_admin`|URI qui doit être consulté dans un navigateur pour pouvoir accéder à l'interface d'administration de la base de données. Cet accès requiert le nom et le mot de passe de l'administrateur indiqués dans la zone `uri`.
`uri_admin_1`|URI d'administration alternatif. Voir `uri_admin`.
`uri_admin_2`|URI d'administration alternatif. Voir `uri_admin`.
`uri_admin_3`|URI d'administration alternatif. Voir `uri_admin`.
`uri_admin_4`|URI d'administration alternatif. Voir `uri_admin`.
`ca_certificate_base64`|Certificat autosigné utilisé pour confirmer qu'une application se connecte au serveur approprié. Le certificat est codé en base64. Vous devez décoder la clé avant de l'utiliser, comme illustré dans le modèle d'application.
`deployment_id`|Identificateur interne du service, créé dans Compose.
`db_type`|Type de base de données fourni par le service, en l'occurrence, `rabbitmq`.
`name`|Nom du déploiement de base de données.
{: caption="Tableau 1. Données d'identification Compose for RabbitMQ" caption-side="top"}
