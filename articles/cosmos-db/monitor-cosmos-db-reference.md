---
title: Informations de référence sur les données de monitoring Azure Cosmos DB | Microsoft Docs
description: Informations de référence sur le journal et les métriques pour la surveillance des données de Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456537"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Informations de référence sur les données de monitoring Azure Cosmos DB
Cet article fournit une référence des données de journal et de métriques collectées pour analyser les performances et la disponibilité d’Azure Cosmos DB. Pour plus d’informations sur la collecte et l’analyse des données de supervision de Cosmos DB, consultez [Surveiller Azure Cosmos DB](monitor-cosmos-db.md).


## <a name="resource-logs"></a>Journaux d’activité de ressources
Le tableau suivant liste les propriétés des journaux de ressources Azure Cosmos DB lorsqu’elles sont collectées dans les journaux Azure Monitor ou dans le Stockage Azure. Dans les journaux Azure Monitor, elles sont collectées dans la table **AzureDiagnostics** avec une valeur **ResourceProvider** définie sur *MICROSOFT. DOCUMENTDB*. 

| Propriété ou champ du stockage Azure | Propriété des journaux Azure Monitor | Description |
| --- | --- | --- |
| **time** | **TimeGenerated** | Date et heure (UTC) de l’opération. |
| **resourceId** | **Ressource** | Le compte Azure Cosmos DB pour lequel les journaux d’activité sont activés.|
| **category** | **Catégorie** | Pour les journaux Azure Cosmos DB, les types de journaux disponibles sont **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption** et **ControlPlaneRequests**. |
| **operationName** | **OperationName** | Nom de l’opération. Cette valeur peut être l’une des opérations suivantes : Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| **properties** | n/a | Le contenu de ce champ est décrit dans les lignes suivantes. |
| **activityId** | **activityId_g** | GUID unique de l’opération journalisée. |
| **userAgent** | **userAgent_s** | Chaîne qui spécifie l’agent utilisateur client effectuant la demande. Le format est {nom de l’agent utilisateur}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Type de la ressource faisant l’objet de l’accès. Cette valeur peut être l’un des types de ressources suivants : Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| **statusCode** | **statusCode_s** | État de réponse de l’opération. |
| **requestResourceId** | **ResourceId** | L’ID de ressource qui se rapporte à la demande. Cette valeur peut pointer vers databaseRid, collectionRid ou documentRid en fonction de l’opération exécutée.|
| **clientIpAddress** | **clientIpAddress_s** | Adresse IP du client. |
| **requestCharge** | **requestCharge_s** | Nombre d’unités de requête utilisées par l’opération. |
| **collectionRid** | **collectionId_s** | ID unique de la collection.|
| **duration** | **duration_s** | Durée de l’opération en millisecondes. |
| **requestLength** | **requestLength_s** | Longueur de la demande, en octets. |
| **responseLength** | **responseLength_s** | Longueur de la réponse, en octets.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Cette valeur n’est pas vide lorsque des [jetons de ressource](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) sont utilisés pour l’authentification. La valeur pointe vers l’ID de ressource de l’utilisateur. |

Pour obtenir la liste de toutes les catégories Azure Monitor et des liens vers les schémas associés, consultez la section consacrée aux [catégories et schémas Azure Monitor](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Mesures
Les tableaux suivants répertorient les métriques de plateforme collectées pour Azure CosmOS DB. Toutes les métriques se trouvent dans l’espace de noms **Métriques standard Cosmos DB**.

Pour obtenir la liste de toutes les métriques de prise en charge d’Azure Monitor (y compris CosmosDB), consultez [Métriques prises en charge avec Azure Monitor](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Métriques de demande
            
|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation) |Description|Dimensions| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Nombre total de requêtes) | Count (Nombre) | Nombre de requêtes effectuées| DatabaseName, CollectionName, Region, StatusCode| Tous | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erreur interne du service, Service indisponible, Requêtes limitées, Requêtes moyennes par seconde | Permet de surveiller les requêtes par code d'état, conteneur à une granularité d'une minute. Pour obtenir les requêtes moyennes par seconde, utilisez l'agrégation Count pour une minute et divisez-la par 60. |
| MetadataRequests (Requêtes de métadonnées) |Count (Nombre) | Nombre de demandes de métadonnées. Azure Cosmos DB gère le conteneur des métadonnées système pour chaque compte, ce qui vous permet d’énumérer les collections, les bases de données, etc., ainsi que leur configuration, et ce gratuitement. | DatabaseName, CollectionName, Region, StatusCode| Tous| |Permet de surveiller les limitations dues aux requêtes de métadonnées.|
| MongoRequests (Requêtes Mongo) | Count (Nombre) | Nombre de requêtes Mongo effectuées | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tous |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Permet de surveiller les erreurs de requête Mongo, les utilisations par type de commande. |

#### <a name="request-unit-metrics"></a>Métriques d’unités de requête

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Frais des requêtes Mongo) | Count (Total) |Unités de requête Mongo consommées| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tous |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Permet de surveiller les unités de requête des ressources Mongo en une minute.|
| TotalRequestUnits (Nombre total d’unités de requête)| Count (Total) | Unités de requête consommées| DatabaseName, CollectionName, Region, StatusCode |Tous| TotalRequestUnits| Permet de surveiller l’utilisation total des unités de requête à une granularité d’une minute. Pour obtenir les unités de requête moyennes consommées par seconde, utilisez l'agrégation Total pour une minute et divisez-la par 60.|
| ProvisionedThroughput (Débit provisionné)| Count (Maximum) |Débit approvisionné à la granularité du conteneur| DatabaseName, ContainerName| 5 Mo| | Permet de surveiller le débit approvisionné par conteneur.|

#### <a name="storage-metrics"></a>Métriques de stockage

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Stockage disponible) |Octets (Total) | Stockage total disponible signalé à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo| Stockage disponible| Permet de surveiller la capacité de stockage disponible (applicable uniquement aux collections de stockage fixe) La granularité minimale doit être de 5 minutes.| 
| DataUsage (Utilisation des données) |Octets (Total) |Utilisation totale des données signalée à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo |Taille des données | Permet de surveiller l'utilisation totale des données d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes.|
| IndexUsage (Utilisation d’index) | Octets (Total) |Utilisation d’index totale signalée à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo| Taille d'index| Permet de surveiller l'utilisation totale des données d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes. |
| DocumentQuota (Quota de document) | Octets (Total) | Quota de stockage total signalé à une granularité de 5 minutes par région.| DatabaseName, CollectionName, Region| 5 Mo |Capacité de stockage| Permet de surveiller le quota total des données d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes.|
| DocumentCount (Nombre de documents) | Count (Total) |Nombre total de documents signalé à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo |Nombre de documents|Permet de surveiller le nombre de documents d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes.|

#### <a name="latency-metrics"></a>Métriques de latence

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Usage |
|---|---|---|---| ---| ---|
| ReplicationLatency (Latence de réplication)| Millisecondes (Minimum, Maximum, Moyenne) | Latence de réplication P99 des régions source et cible pour le compte géolocalisé| SourceRegion, TargetRegion| Tous | Permet de surveiller la latence de réplication P99 entre deux régions pour un compte géorépliqué. |


#### <a name="availability-metrics"></a>Métriques de disponibilité

|Métrique (Nom d’affichage de la métrique) |Unité (Type d’agrégation)|Description| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---|
| ServiceAvailability (Disponibilité du Service)| Pour cent (Minimum, Maximum) | Disponibilité des requêtes de compte à une granularité d’une heure| 1 h | Disponibilité des services | Représente le pourcentage des requêtes totales passées. Une requête échoue en raison d'une erreur système si le code d'état correspond à 410, 500 ou 503 Permet de surveiller la disponibilité du compte à une granularité d'une heure. |


#### <a name="cassandra-api-metrics"></a>Métriques de l'API Cassandra

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Usage |
|---|---|---|---| ---| ---|
| CassandraRequests (Requêtes Cassandra) | Count (Nombre) | Nombre de requêtes d’API Cassandra effectuées| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tous| Permet de surveiller les requêtes Cassandra à une granularité d’une minute. Pour obtenir les requêtes moyennes par seconde, utilisez l'agrégation Count pour une minute et divisez-la par 60.|
| CassandraRequestCharges (Frais des requêtes Cassandra) | Count (Sum, Min, Max, Avg) | Unités de requête consommées par les requêtes d’API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tous| Permet de surveiller les unités de requête utilisées par minute par un compte d’API Cassandra.|
| CassandraConnectionClosures (Fermetures de connexion Cassandra) |Count (Nombre) |Nombre de connexions Cassandra fermées| ClosureReason, Region| Tous | Permet de surveiller la connectivité entre les clients et l’API Cassandra Azure Cosmos DB.|

## <a name="see-also"></a>Voir aussi

- Pour obtenir une description de la supervision Azure Cosmos DB, consultez [Surveiller Azure Cosmos DB](monitor-cosmos-db.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
