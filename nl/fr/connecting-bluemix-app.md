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

# Connexion d'une application {{site.data.keyword.cloud_notm}}

Pour connecter une application {{site.data.keyword.cloud}} à votre service, utilisez les données d'identification créées lors de la mise à disposition du service. Le modèle d'application montre comment utiliser Node.js pour établir une connexion à un service {{site.data.keyword.composeForRabbitMQ_full}} à l'aide des données d'identification fournies et comment créer une base de données, lire dans cette base de données et y écrire.

## Connexion à l'aide du modèle d'application 'Hello World'

Le modèle d'application [compose-rabbitmq-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-rabbitmq-helloworld-nodejs) montre comment utiliser Node.js pour établir une connexion à un service {{site.data.keyword.composeForRabbitMQ}} à l'aide des données d'identification fournies. L'application crée une base de données, y lit et y écrit

Téléchargez le modèle d'application et suivez les instructions contenues dans le fichier Readme. Ensuite, sur la page des détails d'application d'{{site.data.keyword.cloud_notm}}, cliquez sur **Afficher l'application** pour afficher le contenu du tableau *Exemples*.

## Données d'identification disponibles

Nom de zone|Description
----------|-----------
`uri`|URI à utiliser pour établir la connexion au service. Comprend le schéma (`amqps:), le nom et le mot de passe de l'administrateur, le nom d'hôte du serveur, le numéro de port auquel se connecter et le nom vhost.
`uri_direct_1`|Second identificateur URI qui peut être utilisé lors de la connexion au service. Le format est identique à `uri`.
`uri_admin`|URI qui doit être consulté dans un navigateur pour pouvoir accéder à l'interface d'administration de la base de données. Cet accès requiert le nom et le mot de passe de l'administrateur indiqués dans la zone `uri`.
`uri_admin_1`|URI d'administration alternatif. Voir `uri_admin`.
`ca_certificate_base64` `(facultatif)`|Certificat autosigné codé en base64 utilisé pour confirmer qu'une application se connecte au serveur approprié. Le certificat est présent uniquement sur les services ayant un certificat auto-signé au lieu d'un certificat Let's Encrypt. Vous devez décoder la clé avant de l'utiliser, comme illustré dans le modèle d'application.
`deployment_id`|Identificateur interne du service, créé dans Compose.
`db_type`|Type de base de données fourni par le service, en l'occurrence, `rabbitmq`.
`name`|Nom du déploiement de base de données.
{: caption="Tableau 1. Données d'identification Compose for RabbitMQ" caption-side="top"}

**Remarque :** deux portails `haproxy` permettent d'accéder au service RabbitMQ. Les zones `uri` et `uri_direct_1` peuvent toutes deux être utilisées pour établir la connexion. Dans vos applications, utilisez `uri` ou `uri_direct_1` pour gérer les réponses aux pannes de connexion.
