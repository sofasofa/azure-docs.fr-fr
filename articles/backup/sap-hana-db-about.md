---
title: À propos de la sauvegarde de base de données SAP HANA dans les machines virtuelles Azure
description: Cet article explique comment sauvegarder des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: aad9e7e89c54100f460a7f348702d0a59e88f519
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474823"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>À propos de la sauvegarde de base de données SAP HANA dans les machines virtuelles Azure

Les bases de données SAP HANA sont des charges de travail critiques qui nécessitent un objectif de point de récupération (RPO) faible et un objectif de temps de récupération (RTO) rapide. Vous pouvez désormais [sauvegarder des bases de données SAP HANA s’exécutant sur les machines virtuelles Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) à l’aide du service [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview).

Le service Sauvegarde Azure est [certifié Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) par SAP pour la prise en charge de sauvegarde native à l’aide d’API natives de SAP HANA. Cette offre du service Sauvegarde Azure s’aligne sur le mantra de Sauvegarde Azure de sauvegardes d’**infrastructure zéro**, ce qui élimine le besoin de déployer et de gérer l’infrastructure de sauvegarde. Vous pouvez désormais effectuer en toute transparence une sauvegarde et une restauration de bases de données SAP HANA s’exécutant sur des machines virtuelles Azure (les [machines virtuelles de la série M](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#m-series) sont désormais également prises en charge) et tirer parti des fonctionnalités de gestion d’entreprise du service Sauvegarde Azure.

## <a name="added-value"></a>Valeur ajoutée

L’utilisation du service Sauvegarde Azure pour sauvegarder et restaurer des bases de données SAP HANA offre les avantages suivants :

* **Objectif de point de récupération (RPO) de 15 minutes** : La récupération des données critiques jusqu’à 15 minutes est désormais possible.
* **Restaurations dans le temps en un seul clic** : La restauration de données de production sur d’autres serveurs HANA est facilitée. Le chaînage des sauvegardes et des catalogues pour effectuer des restaurations est entièrement géré par Azure en arrière-plan.
* **Conservation à long terme** : Pour des besoins stricts de conformité et d’audit. Conservez vos sauvegardes pendant des années, en fonction de la durée de conservation au-delà de laquelle les points de récupération sont nettoyés automatiquement par la fonctionnalité intégrée de gestion du cycle de vie.
* **Gestion des sauvegardes à partir d’Azure** : Utilisez les fonctionnalités de gestion et de surveillance du service Sauvegarde Azure pour améliorer l’expérience de gestion. Azure CLI est également pris en charge.

Pour afficher les scénarios de sauvegarde et de restauration que nous prenons en charge aujourd’hui, reportez-vous à la [matrice de prise en charge du scénario SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architecture de sauvegarde

![Diagramme de l’architecture de sauvegarde](./media/sap-hana-db-about/backup-architecture.png)

* Le processus de sauvegarde commence par la [création d’un coffre Recovery Services](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) dans Azure. Ce coffre sera utilisé pour stocker les sauvegardes et les points de récupération créés au fil du temps.
* La machine virtuelle Azure exécutant le serveur SAP HANA est inscrite auprès du coffre, et les bases de données à sauvegarder sont [découvertes](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Pour permettre au service Sauvegarde Azure de découvrir les bases de données, un [script de préinscription](https://aka.ms/scriptforpermsonhana) doit être exécuté sur le serveur HANA en tant qu’utilisateur racine.
* Ce script crée un utilisateur de base de données **AZUREWLBACKUPHANAUSER** et une clé correspondante du même nom dans **hdbuserstore**. Pour en savoir plus sur ce que fait le script, voir la [section Configuration des autorisations](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions).
* Le service Sauvegarde Azure installe à présent le **plug-in Sauvegarde Azure pour HANA** sur le serveur SAP HANA inscrit.
* Le **plug-in Sauvegarde Azure pour HANA** se sert de l’utilisateur de base de données **AZUREWLBACKUPHANAUSER** créé par le script de préinscription pour effectuer toutes les opérations de sauvegarde et de restauration. Si vous tentez de configurer une sauvegarde pour des bases de données SAP HANA sans exécuter ce script, il se peut que l’erreur suivante s’affiche : **UserErrorHanaScriptNotRun**.
* Pour [configurer une sauvegarde](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) sur les bases de données découvertes, choisissez la stratégie de sauvegarde requise et activez les sauvegardes.

* Une fois la sauvegarde configurée, le service Sauvegarde Azure configure les paramètres Backint suivants au niveau de la BASE DE DONNÉES sur le serveur de SAP HANA protégé :
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Assurez-vous que ces paramètres ne sont *pas* présents au niveau de HÔTE. Les paramètres au niveau de l’hôte remplacent ces paramètres et peuvent entraîner un comportement inattendu.
>

* Le **plug-in Sauvegarde Azure pour HANA** conserve l’ensemble des planifications de sauvegarde et des détails de la stratégie. Il déclenche les sauvegardes planifiées et communique avec le **Moteur de sauvegarde HANA** via les API Backint.
* Le **Moteur de sauvegarde HANA** retourne un flux Backint avec les données à sauvegarder.
* Les sauvegardes planifiées et à la demande (déclenchées à partir du portail Azure) complètes ou différentielles sont toutes initiées par le **plug-in Sauvegarde Azure pour HANA**. Toutefois, les sauvegardes de fichier journal sont gérées et déclenchées par le **Moteur de sauvegarde HANA**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Restaurer une base de données SAP HANA s’exécutant sur une machine virtuelle Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Découvrez comment [gérer des bases de données SAP HANA sauvegardées à l’aide de Sauvegarde Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
