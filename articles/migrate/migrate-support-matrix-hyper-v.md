---
title: Prise en charge de l’évaluation Hyper-V dans Azure Migrate
description: Découvrez la prise en charge de l’évaluation Hyper-V avec Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834465"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Tableau de prise en charge pour l’évaluation Hyper-V

Cet article résume les paramètres et les limites de l’évaluation de machines virtuelles Hyper-V avec [Azure Migrate : Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Si vous recherchez des informations sur la migration de machines virtuelles Hyper-V vers Azure, consultez le [tableau de prise en charge de la migration](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Vue d’ensemble

Pour évaluer des machines locales pour une migration vers Azure avec cet article, vous ajoutez l’outil Azure Migrate : Server Assessment à un projet Azure Migrate. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure. Après la découverte des machines, vous rassemblez les machines découvertes dans des groupes et effectuez l’évaluation d’un groupe.


## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation**| Découvrez et évaluez jusqu’à 35 000 machines virtuelles Hyper-V au sein d’un même [projet](migrate-support-matrix.md#azure-migrate-projects).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des machines virtuelles VMware et Hyper-V, et des serveurs physiques, jusqu’aux limites d’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 5 000 machines virtuelles Hyper-V.<br/><br/> L’appliance peut se connecter à jusqu’à 300 hôtes Hyper-V.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35,000 machines virtuelles par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.



## <a name="hyper-v-host-requirements"></a>Configuration requise pour l’hôte Hyper-V

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement de l’hôte**       | L'hôte Hyper-V peut être autonome ou déployé dans un cluster. |
| **autorisations**           | Vous avez besoin des droits d'administrateur sur l'hôte Hyper-V. <br/> Sinon, si vous ne souhaitez pas attribuer d’autorisations d’administrateur, créez un compte d’utilisateur local ou de domaine et ajoutez l’utilisateur à ces groupes/utilisateurs de gestion à distance, administrateurs Hyper-V et utilisateurs de l’analyseur de performances. |
| **Système d'exploitation hôte** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/> Vous ne pouvez pas évaluer des machines virtuelles situées sur des hôtes Hyper-V exécutant Windows Server 2012. |
| **Communication à distance PowerShell**   | Doit être activée sur chaque hôte. |
| **Réplica Hyper-V**       | Si vous utilisez Hyper-V Replica (ou si vous avez plusieurs machines virtuelles avec les mêmes identificateurs de machines virtuelles) et que vous découvrez les machines virtuelles originales et répliquées à l'aide d'Azure Migrate, l'évaluation générée par Azure Migrate pourrait ne pas être exacte. |


## <a name="hyper-v-vm-requirements"></a>Configuration requise pour la machine virtuelle Hyper-V

| **Support**                  | **Détails**               
| :----------------------------- | :------------------- |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure. |
| **Services d’intégration**       | Les [services d'intégration Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) doivent fonctionner sur les machines virtuelles que vous évaluez afin de capturer les informations du système d'exploitation. |


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. L’appliance pour Hyper-V fonctionne sur une machine virtuelle Hyper-V et est déployée à l’aide d’un disque dur virtuel (VHD) Hyper-V compressé téléchargé depuis le portail Azure. 

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---hyper-v) pour Hyper-V.
- En savoir plus sur les [URL](migrate-appliance.md#url-access) auxquelles l’appliance doit accéder.

## <a name="port-access"></a>Accès au port

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ``` https://<appliance-ip-or-name>:44368 ```<br/> Connexions sortantes sur les ports 443 (HTTPS), 5671 et 5672 (AQMP) pour l’envoi de métadonnées de découverte et de performances à Azure Migrate.
**Hôte/cluster Hyper-V** | Connexions entrantes sur les ports WinRM 5985 (HTTP) et 5986 (HTTPS) pour extraire les métadonnées de configuration et de performance des machines virtuelles Hyper-V en utilisant une session Common Information Model (CIM).

## <a name="agent-based-dependency-visualization"></a>Visualisation des dépendances basée sur les agents

La [visualisation des dépendances](concepts-dependency-visualization.md) vous permet de visualiser les dépendances entre les machines que vous souhaitez évaluer et migrer. Les exigences et limitations applicables à la visualisation basée sur les agents sont résumées dans le tableau suivant


**Prérequis** | **Détails**
--- | ---
**Déploiement** | Avant de déployer la visualisation des dépendances, vous devez disposer d’un projet Azure Migrate, avec l'outil Azure Migrate : Server Assessment ajouté au projet. Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales.<br/><br/> La visualisation des dépendances n’est pas disponible dans Azure Government.
**Service Map** | La visualisation des dépendances basée sur les agents utilise la solution [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) dans les [journaux Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Pour déployer, vous associez un espace de travail Log Analytics nouveau ou existant à un projet Azure Migrate.
**Espace de travail Log Analytics** | L’espace de travail doit se trouver dans le même abonnement que le projet Azure Migrate.<br/><br/> Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest.<br/><br/>  L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté.
**Frais** | La solution Service Map n’entraîne pas de frais pendant les 180 premiers jours (à compter du jour où vous associez l’espace de travail Log Analytics au projet Azure Migrate).<br/><br/> Au bout de 180 jours, des frais Log Analytics standard s’appliquent.<br/><br/> L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics associé entraîne des frais Log Analytics standard.<br/><br/> Si vous supprimez le projet Azure Migrate, l’espace de travail n’est pas supprimé avec celui-ci. Une fois le projet supprimé, l’utilisation de Service Map n’est pas gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.
**Agents** | La visualisation des dépendances basée sur les agents nécessite l’installation de deux agents sur chaque machine à analyser.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Agent de dépendances](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Connectivité Internet** | Si les machines ne sont pas connectées à Internet, vous devez installer la passerelle Log Analytics sur celles-ci.


## <a name="next-steps"></a>Étapes suivantes

[Préparer l’évaluation des machines virtuelles Hyper-V](tutorial-prepare-hyper-v.md)
