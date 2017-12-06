---

copyright:
  years: 2016,2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Initiation à Compose for RabbitMQ
{: #getting-started-with-compose-for-rabbitmq}

RabbitMQ gère de façon asynchrone les messages qui transitent entre vos applications et des bases de données, permettant ainsi de séparer les données et les couches d'application. RabbitMQ permet aux développeurs d'acheminer, de suivre et de placer en file d'attente des messages avec des niveaux de persistance et des paramètres de distribution personnalisables, ainsi qu'une publication confirmée. {{site.data.keyword.composeForRabbitMQ_full}} vous permet d'accéder à l'interface d'administration conviviale avec un hôte de fonctions de gestion, telles que la surveillance du déploiement, la mise à l'échelle en un seul clic, la configuration utilisateur et l'accès aux fichiers journaux.
{:shortdesc}

**Remarque :** les instances de service Compose qui ont été mises à disposition avant le 14 septembre 2016 et qui sont toujours actives peuvent encore être utilisées et sont directement accessibles sur le site [https://www.compose.com/](https://www.compose.com). Les instances de service Compose mises à disposition depuis cette date sont directement accessibles et utilisables dans votre compte {{site.data.keyword.cloud}}.

## Création d'une instance de service Compose for RabbitMQ

[Créez une instance {{site.data.keyword.composeForRabbitMQ}}](https://console.ng.bluemix.net/catalog/services/compose-for-rabbitmq/).

Lorsque vous créez une instance du service, prenez soin de sélectionner un nom pour votre service et un nom de données d'identification. Laissez le service non lié ; vous pourrez connecter une application à votre service ultérieurement en utilisant les données d'identification fournies lors de la mise à disposition du service.  La section *Données d'identification disponibles* répertorie les différentes valeurs de données d'identification.

Lorsque vous mettez votre instance {{site.data.keyword.composeForRabbitMQ}} à disposition, vous avez le choix entre le plan *Standard* et le plan *Entreprise*. Avec le plan *Entreprise*, vous pouvez mettre votre instance {{site.data.keyword.composeForRabbitMQ}} à disposition dans un cluster {{site.data.keyword.composeEnterprise}} disponible. {{site.data.keyword.composeEnterprise}} fournit la sécurité et l'isolement requis par les normes de conformité des entreprises et utilise un réseau dédié pour garantir les performances des bases de données déployées. Pour plus d'informations, voir la [documentation Compose - Entreprise](../ComposeEnterprise/index.html).

## Gestion de Compose for RabbitMQ

Vous pouvez gérer votre service depuis son tableau de bord. Vous y trouverez des informations concernant la base de données Compose d'{{site.data.keyword.cloud_notm}} et la manière de vous y connecter. Vous pouvez également :
- gérer vos sauvegardes ; 
- allouer plus de ressources à votre service ; 
- modifier le mot de passe du service ;
- constituer des listes blanches pour limiter l'accès à vos bases de données.
Pour plus d'informations, voir [Paramètres](./dashboard-settings.html).

## Connexion à Compose for RabbitMQ

Vous pouvez vous connecter à votre service avec les données d'identification créées en même temps que le service ou avec les chaînes de connexion et la ligne de commande fournies dans l'onglet *Vue d'ensemble* du tableau de bord de votre service.

## Connexion d'une application {{site.data.keyword.cloud_notm}} à Compose for RabbitMQ

Pour connecter une application {{site.data.keyword.cloud_notm}} à votre service, utilisez les données d'identification créées en même temps que le service. Pour plus d'informations sur la connexion d'une application {{site.data.keyword.cloud_notm}} à un service {{site.data.keyword.composeForRabbitMQ}}, voir [Connexion d'une application {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Connexion à Compose for RabbitMQ depuis l'extérieur d'{{site.data.keyword.cloud_notm}}

Pour vous connecter à {{site.data.keyword.composeForRabbitMQ}} depuis l'extérieur d'{{site.data.keyword.cloud_notm}}, vous pouvez utiliser les chaînes de connexion ou la ligne de commande fournies. Pour plus d'informations sur ce type de connexion, voir [Connexion d'une application externe](./connecting-external.html).
