---
title: 'Azure Arc pour serveurs (préversion) : vue d’ensemble'
description: Découvrez comment utiliser Azure Arc pour serveurs afin de gérer les machines hébergées en dehors d’Azure comme s’il s’agissait d’une ressource Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, Update Management, Change Tracking, inventaire, runbooks, Python, graphique, hybride
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: b0f1d235391c4c4e3804a6dccc8174e946035b6a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899201"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Présentation d’Azure Arc pour serveurs (préversion)

Azure Arc pour serveurs (préversion) vous permet de gérer vos machines Windows et Linux hébergées en dehors d’Azure sur votre réseau d’entreprise ou un autre fournisseur de cloud, de la même façon que vous gérez des machines virtuelles Azure natives. Quand une machine hybride est connectée à Azure, elle devient une machine connectée et est traitée comme une ressource dans Azure. Chaque machine connectée possède un ID de ressource, est gérée dans le cadre d’un groupe de ressources au sein d’un abonnement et tire parti des constructions Azure standard, comme Azure Policy et l’application d’étiquettes.

Pour bénéficier de cette expérience avec vos machines hybrides hébergées en dehors d’Azure, vous devez installer Azure Connected Machine sur chaque machine que vous envisagez de connecter à Azure. Cet agent ne fournit aucune autre fonctionnalité et ne remplace pas l’agent Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez superviser de manière proactive le système d’exploitation et les charges de travail en cours d’exécution sur la machine, gérer le système d’exploitation à l’aide de runbooks Automation ou de solutions comme Update Management ou utiliser d’autres services Azure tels qu’[Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Cette préversion est conçue à des fins d’évaluation et nous vous recommandons de ne pas gérer les machines de production critiques.
>

## <a name="supported-scenarios"></a>Scénarios pris en charge

Azure Arc pour serveurs (préversion) prend en charge les scénarios suivants avec les ordinateurs connectés :

- Affecter des [configurations invité Azure Policy](../../governance/policy/concepts/guest-configuration.md) à l’aide de la même expérience que lors de l’attribution de stratégie pour des machines virtuelles Azure.
- Les données de journal collectées par l’agent Log Analytics et stockées dans l’espace de travail Log Analytics auprès duquel la machine est inscrite contiennent maintenant des propriétés spécifiques à la machine, telles que l’ID de ressource, qui peuvent être utilisées pour prendre en charge l’accès aux journaux en fonction du [contexte des ressources](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Régions prises en charge

Avec Azure Arc pour serveurs (préversion), seules certaines régions sont prises en charge :

- WestUS2
- WestEurope
- WestAsia

## <a name="prerequisites"></a>Conditions préalables requises

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les versions suivantes des systèmes d’exploitation Windows et Linux sont officiellement prises en charge pour l’agent Azure Connected Machine : 

- Windows Server 2012 R2 et versions ultérieures
- Ubuntu 16.04 et 18.04

>[!NOTE]
>Cette préversion de l’agent Connected Machine pour Windows ne prend en charge que Windows Server configuré pour utiliser la langue anglaise.
>

### <a name="azure-subscription-and-service-limits"></a>Limites du service et de l’abonnement Azure

Avant de configurer vos machines avec Azure Arc pour serveurs (préversion), vous devez passer en revue les [limites de l’abonnement](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) et les [limites des groupes de ressources](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) Azure Resource Manager pour planifier le nombre de machines à connecter.

### <a name="networking-configuration"></a>Configuration de la mise en réseau

L’agent Connected Machine pour Linux et Windows communique en sortie de manière sécurisée vers Azure Arc sur le port TCP 443. Si la machine se connecte via un pare-feu ou un serveur proxy pour communiquer sur Internet, consultez les exigences ci-dessous pour connaître la configuration réseau nécessaire.

Si la connectivité sortante est restreinte par votre pare-feu ou votre serveur proxy, vérifiez que les URL listées ci-dessous ne sont pas bloquées. Si vous autorisez uniquement les plages d’adresses IP ou les noms de domaine nécessaires pour que l’agent communique avec le service, vous devez également autoriser l’accès aux URL et étiquettes de service suivantes.

Balises de service :

- AzureActiveDirectory
- AzureTrafficManager

URL :

| Ressource de l’agent | Description |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Guest Configuration|
|*-agentservice-prod-1.azure-automation.net|Guest Configuration|
|*.his.hybridcompute.azure-automation.net|Service d’identité hybride|

Pour obtenir la liste d’adresses IP de chaque balise de service/région, consultez le fichier JSON - [Plages d’adresses IP Azure et balises de service – Cloud Public](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publie chaque semaine une mise à jour contenant chacun des services Azure et les plages d’adresses IP qu’il utilise. Pour plus d’informations, consultez [Étiquettes de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Les URL indiquées dans le tableau précédent sont nécessaires en plus des informations relatives à la plage d’adresses IP des balises de service, car la plupart des services n’ont pas actuellement d’inscription de balise de service. Ainsi, les adresses IP sont sujettes à modification. Si des plages d’adresses IP sont requises par la configuration de votre pare-feu, utilisez l’étiquette de service **AzureCloud** pour autoriser l’accès à tous les services Azure. Ne désactivez pas la supervision ou l’inspection de la sécurité de ces URL ; autorisez-les comme vous le feriez pour tout autre trafic Internet.

### <a name="register-azure-resource-providers"></a>Inscrire des fournisseurs de ressources Azure

Azure Arc pour serveurs (préversion) dépend des fournisseurs de ressources Azure suivants dans votre abonnement pour pouvoir utiliser ce service :

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

S’ils ne sont pas inscrits, vous pouvez les inscrire à l’aide des commandes suivantes :

Azure PowerShell :

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI :

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Vous pouvez également inscrire les fournisseurs de ressources dans le portail Azure en suivant les étapes décrites sous [Portail Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Agent Connected Machine

Vous pouvez télécharger le package de l’agent Azure Connected Machine pour Windows et Linux à partir des points listés ci-dessous.

- [Package Windows Installer de l’agent pour Windows](https://aka.ms/AzureConnectedMachineAgent) à partir du centre de téléchargement Microsoft.
- Le package de l’agent pour Linux est distribué à partir du [dépôt de packages](https://packages.microsoft.com/) de Microsoft à l’aide du format de package préféré pour la distribution (.RPM ou .DEB).

>[!NOTE]
>Pendant cette préversion, un seul package a été publié, qui convient pour Ubuntu 16.04 ou 18.04.

## <a name="install-and-configure-agent"></a>Installer et configurer l’agent

Selon vos besoins, plusieurs méthodes vous permettent de connecter des machines de votre environnement hybride directement à Azure. Le tableau suivant décrit chacune d’entre elle, pour vous permettre d’identifier la plus adaptée à votre organisation.

| Méthode | Description |
|--------|-------------|
| de manière interactive, | Installer manuellement l’agent sur une seule machine ou un petit nombre de machines en suivant les étapes décrites dans [Connecter des machines à partir du portail Azure](quickstart-onboard-portal.md).<br> À partir du portail Azure, vous pouvez générer un script et l’exécuter sur la machine pour automatiser les étapes d’installation et de configuration de l’agent.|
| À grande échelle | Installer et configurer l’agent pour plusieurs machines en suivant les instructions indiquées dans [Connecter des machines à l’aide d’un principal de service](quickstart-onboard-powershell.md).<br> Cette méthode crée un principal de service pour connecter des machines de manière non interactive.|


## <a name="next-steps"></a>Étapes suivantes

- Pour commencer l’évaluation d’Azure Arc pour serveurs (préversion), suivez l’article [Connecter des machines hybrides à Azure à partir du portail Azure](quickstart-onboard-portal.md). 