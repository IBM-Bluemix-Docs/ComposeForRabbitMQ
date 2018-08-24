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

# Sauvegardes
{: #backups}

Vous pouvez créer et télécharger des sauvegardes à partir de l'onglet _Backups_ de la page de gestion du tableau de bord de votre service. Vous avez le choix entre les sauvegardes quotidiennes, hebdomadaires, mensuelles et à la demande. Elles sont conservées selon la planification suivante :

Type de sauvegarde|Planification de conservation
----------|-----------
Quotidienne|Les sauvegardes quotidiennes sont conservées pendant 7 jours
Hebdomadaire|Les sauvegardes hebdomadaires sont conservées pendant 4 semaines
Mensuelle|Les sauvegardes mensuelles sont conservées pendant 3 mois
A la demande|Une seule sauvegarde à la demande est conservée. Il s'agit toujours de la dernière sauvegarde à la demande effectuée.
{: caption="Tableau 1. Planification de conservation des sauvegardes" caption-side="top"}

Les planifications de sauvegarde et les règles de conservation sont fixées. Si vous avez besoin de conserver davantage de sauvegardes que ne le permet la planification de conservation, vous devez télécharger les sauvegardes et les archiver en fonction de vos exigences métier.

## Affichage des sauvegardes existantes

Des sauvegardes quotidiennes de votre base de données sont automatiquement planifiées. Pour afficher vos sauvegardes existantes, accédez à la page de gestion du tableau de bord de votre service. 

  ![Sauvegardes](./images/rabbitmq-backups-show.png "Liste des sauvegardes dans le tableau de bord du service")

Cliquez sur la ligne correspondante pour développer les options de chaque sauvegarde disponible.

  ![Options de sauvegarde](./images/rabbitmq-backups-options.png "Options d'une sauvegarde.") 

### Utilisation de l'API pour afficher les sauvegardes existantes

Une liste des sauvegardes est disponible sur le noeud final `GET /2016-07/deployments/:id/backups`. L'ID d'instance de service et l'ID de déploiement du noeud final Foundation apparaissent tous les deux dans la page de vue d'ensemble du service. Par exemple : 
``` 
https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
```  

## Création d'une sauvegarde à la demande

Outre les sauvegardes planifiées, vous pouvez créer une sauvegarde manuelle. Pour créer une sauvegarde manuelle, accédez à la page de gestion du tableau de bord de votre service et cliquez sur *Backup now*.

### Utilisation de l'API pour la création d'une sauvegarde

Envoyez une demande POST au noeud final des sauvegardes pour initier une sauvegarde manuelle : `POST /2016-07/deployments/:id/backups`. Il renvoie immédiatement l'ID de la recette et des informations sur la sauvegarde lors de son exécution. Il vous appartient de vérifier dans le noeud final des sauvegardes si la sauvegarde est terminée et de rechercher son backup_id avant de l'utiliser. Utilisez `GET /2016-07/deployments/:id/backups/`.

## Téléchargement d'une sauvegarde

Pour télécharger une sauvegarde, accédez à la page de gestion du tableau de bord de votre service et cliquez sur *Download* sur la ligne correspondant à la sauvegarde que vous voulez télécharger.

### Utilisation de l'API pour le téléchargement d'une sauvegarde

Recherchez la sauvegarde à restaurer dans la page _Backups_ de votre service et copiez le backup_id ou utilisez `GET /2016-07/deployments/:id/backups` pour rechercher une sauvegarde et son backup_id via l'API Compose. Utilisez ensuite l'élément backup_id pour trouver des informations et le lien de téléchargement concernant une sauvegarde spécifique : `GET /2016-07/deployments/:id/backups/:backup_id`.

## Contenu de sauvegarde

Les sauvegardes RabbitMQ sont une représentation JSON des métadonnées du courtier. Elles sont créées à partir d'une commande d'exportation fournie par le plug-in de gestion de RabbitMQ. L'exécution de l'exportation sur votre service n'a aucune répercussion sur les performances.

## Utilisation d'une sauvegarde avec une base de données locale

Vous pouvez utiliser votre sauvegarde {{site.data.keyword.composeForRabbitMQ}} pour exécuter une copie locale de votre base de données.

Vous aurez besoin d'une instance locale RabbitMQ en cours d'exécution, avec le plug-in de gestion inclus dans la distribution RabbitMQ. Activez-le avec `rabbitmq-plugins enable rabbitmq_management`. Avec cette ligne de commande vous obtenez en plus :

* l'identificateur unique de l'administrateur à l'adresse `http://localhost:15672/`,
* l'API HTTP à l'adresse `http://server-name:15672/api/`,
* et l'outil de ligne de commande de l'API `rabbitmqadmin` à l'adresse `http://localhost:15672/cli/ `.

Pour importer le fichier de sauvegarde JSON, vous pouvez :

* par le biais de l'identificateur unique de l'administrateur à http://localhost:15672/, utiliser la fonction d'_importation/exportation de définitions_ proposée au bas de la page de vue d'ensemble.
* par le biais de l'API, envoyer un POST à `http://server-name:15672/api/definitions`, par exemple :
```http
curl -i -u guest:guest -H "content-type:application/json" -X POST --data @<path_to_your_rabbitmq_backup> http://localhost:15672/api/definitions
```
* utiliser la commande `rabbitmqadmin import <your_rabbitmq_backup>`.

## Restauration d'une sauvegarde

Pour restaurer une sauvegarde sur une nouvelle instance de service, suivez la procédure d'affichage des sauvegardes, puis cliquez sur la ligne correspondante afin de développer les options de la sauvegarde que vous voulez télécharger. Cliquez sur le bouton **Restore**. Un message vous indiquant qu'une restauration a été initiée s'affiche. La nouvelle instance de service sera automatiquement nommée "rabbitmq-restore-[timestamp]" ; elle s'affiche dans votre tableau de bord au démarrage de la mise à disposition.

### Restauration via l'interface de ligne de commande {{site.data.keyword.cloud_notm}}

Procédez comme suit pour restaurer une sauvegarde d'un service RabbitMQ en cours d'exécution vers un nouveau service RabbitMQ à l'aide de l'interface de ligne de commande {{site.data.keyword.cloud_notm}}. 
1. Le cas échéant, [téléchargez et installez-la](https://console.{DomainName}/docs/cli/index.html#overview). 
2. Recherchez la sauvegarde à restaurer dans la page _Backups_ de votre service et copiez son ID.  
  **Ou**  
  Utilisez `GET /2016-07/deployments/:id/backups` pour rechercher une sauvegarde et son ID grâce à l'API Compose. Le noeud final Foundation et l'ID d'instance de service s'affichent tous les deux dans la page de vue d'ensemble du service. Par exemple : 
  ``` 
  https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
  ```  
  La réponse inclut la liste de toutes les sauvegardes disponibles pour cette instance de service. Choisissez la sauvegarde à partir de laquelle procéder à la restauration et copiez son ID.

3. Connectez-vous avec le compte et les données d'identification appropriés : `ibmcloud login` (ou `ibmcloud login -help` pour afficher toutes les options de connexion).

4. Accédez à votre organisation et votre espace : `ibmcloud target -o "$YOUR_ORG" -s "YOUR_SPACE"`

5. Utilisez la commande `service create` pour mettre à disposition un nouveau service et indiquez le source source et la sauvegarde spécifique que vous restaurez dans un objet JSON. Par exemple :
``` 
ibmcloud service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": "$BACKUP_ID" }'
```
  _SERVICE_ doit avoir pour valeur compose-for-rabbitmq et _PLAN_ doit avoir pour valeur Standard ou Enterprise, en fonction de votre environnement. Pour _SERVICE\_INSTANCE\_NAME_, indiquez le nom de votre nouveau service. _source\_service\_instance\_id_ correspond à l'ID d'instance de service de la source de la sauvegarde ; pour l'obtenir, exécutez `ibmcloud cf service DISPLAY_NAME --guid` où _DISPLAY\_NAME_ est le nom du service RabbitMQ à l'origine de la sauvegarde. 
  
  
### Migration vers une nouvelle version

Certaines mises à niveau de version principale ne sont pas disponibles dans le déploiement en cours d'exécution. Vous devez alors mettre à disposition un nouveau service qui exécute la version mise à niveau, puis migrer vos données à l'aide d'une sauvegarde. Ce processus est identique à la restauration d'une sauvegarde décrite précédemment, mais vous devez indiquer la version vers laquelle vous souhaitez effectuer la mise à niveau.

``` 
ibmcloud service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": ""$BACKUP_ID", "db_version":"$VERSION_NUMBER" }'
```

Par exemple, la restauration d'une version plus ancienne d'un service {{site.data.keyword.composeForRabbitMQ}} vers un nouveau service exécutant RabbitMQ 3.6.14 est similaire à l'exemple suivant :
```
ibmcloud service create compose-for-rabbitmq Standard migrated_rabbit -c '{ "source_service_instance_id": "0269e284-dcac-4618-89a7-f79e3f1cea6a", "backup_id":"5a96d8a7e16c090018884566", "db_version":"3.6.14"  }'

