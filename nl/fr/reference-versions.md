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

# Versions 
{: #facts-and-figures}

## Versions (prises en charge et déployées)

Versions déployables| Version préférée
----------|-----------
3.6.14, 3.7.5 | 3.7.5
{: caption="Tableau 1. Versions de RabbitMQ" caption-side="top"}

## Version préférée

La version préférée est généralement la version la plus récente de RabbitMQ disponible pour {{site.data.keyword.composeForRabbitMQ}}. Il s'agit de la version par défaut dans la liste déroulante du sélecteur de version sur la page du catalogue. C'est également la version automatiquement mise à disposition par l'API lorsqu'aucune version n'est spécifiée dans l'appel.

### Nouveau protocole de version préférée

Lorsqu'une nouvelle version est disponible, sa publication est annoncée et elle devient disponible pour déploiement. Après publication un délai d'environ 7 jours s'écoule pendant lequel la nouvelle version est disponible sans être la version préférée. Ce délai permet aux utilisateurs de déployer et tester la nouvelle version tout en disposant toujours de la version en cours. Au bout de ce délai de 7 jours, la nouvelle version est définie en tant que version préférée ou une nouvelle date de modification est annoncée.

Vous trouverez la liste des versions disponibles pour mise à disposition sur la {{site.data.keyword.composeForRabbitMQ}} [page du catalogue](https://console.{DomainName}/catalog/services/compose-for-rabbitmq).

Pour obtenir la liste des versions actuellement disponibles pour votre service {{site.data.keyword.composeForRedis}}, vous pouvez utiliser le
noeud final [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions).
