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

# Architecture 
{: #architecture}

## Réplication

Un service {{site.data.keyword.composeForRabbitMQ_full}} se compose de trois noeuds qui fonctionnent unanimement en tant que courtier hautement disponibles, chacun exécutant l'application RabbitMQ et se partageant les utilisateurs, les hôtes virtuels, les files d'attente, les échanges, les liaisons et les paramètres d'exécution. Lorsque vous sélectionnez une région dans laquelle le service est déployé, les trois noeuds se propagent dans les zones de disponibilité de la région et toutes les données et états requis pour le fonctionnement d'un courtier RabbitMQ sont répliqués sur tous les noeuds. 

Un noeud fait office de maître, les autres faisant office de miroirs. Les miroirs appliquent les opérations qui se produisent sur le maître exactement dans le même ordre que le maître et conservent donc le même état. Toutes les actions autres qu'une publication vont uniquement au maître qui diffuse ensuite les effets des actions aux miroirs. Si le maître est en échec, l'un des miroir est promu et le courtier RabbitMQ functionne normalement.

## Chiffrement de disque

Tous les services {{site.data.keyword.composeForRabbitMQ}} disposent du chiffrement au repos. Vos données résident sur des serveurs où le chiffrement au niveau volume est activé. 

Etant donné que {{site.data.keyword.composeForRabbitMQ}} est un service géré, les opérateurs {{site.data.keyword.cloud}} Compose peuvent voir les données. Outre le chiffrement du disque, nous vous recommandons, si vous transférez des informations personnelles via votre file d'attente de messagerie, de chiffrer vos informations avant de les envoyer ou, à l'aide d'extensions ou de fonctions, d'activer le chiffrement sur RabbitMQ. Même si ces méthodes risquent d'avoir un impact sur la facilité d'utilisation ou les performances, il est conseillé de s'assurer que les informations personnelles sont protégées par un chiffrement.


## Mise à l'échelle automatique

Les ressources sont automatiquement mises à l'échelle en fonction de l'utilisation totale de la mémoire du courtier RabbitMQ. Le stockage est basé sur un rapport de mémoire RAM mise à disposition de 1 sur 1. Ces ressources sont intégrées en tant qu'unités.

La mise à l'échelle automatique est conçue pour répondre aux tendances à court et moyen termes de votre base de données. Votre service est contrôlé toutes les minutes et, s'il est à court de mémoire RAM, des unités supplémentaires sont attribuées au déploiement. 

Une mise à l'échelle automatique ne réduit pas la taille des déploiements où l'utilisation du disque/de la mémoire à diminué. Les ressources mises à disposition de vos bases de données sont conservées pour les besoins ultérieurs ou jusqu'à ce que vous réduisiez manuellement votre déploiement.
{: .tip}
