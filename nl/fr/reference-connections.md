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

# Configuration de connexion
{: #connection-configuration}

Les connexions à la base de données {{site.data.keyword.composeForRabbitMQ_full}} sont gérées par 2 portails HAProxy. Chaque portail dispose de 64 Mo de mémoire.

Disposer de deux portails permet aux applications de conserver une connectivité lorsque l'un des portails n'est plus accessible. Le basculement côté client relève de la responsabilité du concepteur d'applications. A moins que vous n'utilisiez un pilote qui traite entièrement les erreurs de basculement, il est conseillé de procéder comme suit :

* Utilisez un pilote qui accepte plusieurs noeuds finaux dans sa configuration de connexion.
* Vérifiez que les appels de vos applications en vue d'une lecture ou d'une écriture dans la base de données réagissent aux erreurs de manière appropriée. Ces opérations incluent :
  + la journalisation de l'erreur et la reconnexion,
  + la reconnexion et une nouvelle tentative de l'opération à l'origine de l'erreur,
  + la mise en file d'attente de l'opération ayant échoué et la planification de la reconnexion.

## Chiffrement du transport

Tous les portails {{site.data.keyword.composeForRabbitMQ}} HAProxy disposent de TLS/SSL et prennent en charge TLS version 1.2. Les certificats du service sont des certificats Let's Encrypt.

## Limites de connexion

Les connexions de base de données sont limitées à 1000 connexions par portail. Lorsque la limite de connexion est dépassée, le service ne répond plus. Vous pouvez gérer les connexions à partir de votre application à l'aide de la fonction de regroupement de connexions de votre pilote.

## Délais d'attente de proxy

Les portails HAProxy gèrent le cycle de vie des connexions entre le serveur et le client. Nous en fournissons le détail à titre de référence pour les éditeurs de routeurs et les personnes intéressées par les activités de bas niveau des connexions de base de données {{site.data.keyword.composeForEtcd}}.

Paramètre | Valeur | S'applique
----------|-----------|-----------
client | 3h | Lorsque le client doit accuser réception de données ou en envoyer.
connect | 10s | Lorsqu'une connexion est établie entre le proxy et le serveur.
server | 3h | Lorsque le serveur doit accuser réception de données ou en envoyer.
check | 5s | En tant que vérification secondaire du délai d'attente lorsqu'une connexion est établie entre le proxy et le serveur.
{: caption="Tableau 1. Délais d'attente des portails HAProxy RabbitMQ" caption-side="top"}




