---
title: Restaurer des applications supprimées
description: Découvrez comment restaurer une application supprimée dans Azure App Service. Évitez les problèmes liés à la suppression accidentelle d’une application.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689621"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurer une application App Service supprimée à l’aide de PowerShell

Si vous avez supprimé accidentellement votre application dans Azure App Service, vous pouvez la restaurer à l’aide des commandes du [module PowerShell Az](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Les applications supprimées sont purgées du système 30 jours après la suppression initiale. Une fois qu’une application a été purgée, il n’est plus possible de la récupérer.
>

## <a name="re-register-app-service-resource-provider"></a>Réinscrire le fournisseur de ressources App Service
Certains clients peuvent rencontrer un problème entraînant l’échec de la récupération de la liste des applications supprimées. Pour résoudre le problème, exécutez la commande suivante :

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lister les applications supprimées

Pour obtenir la collection des applications supprimées, vous pouvez utiliser `Get-AzDeletedWebApp`.

Pour plus d’informations sur une application supprimée spécifique, vous pouvez utiliser :

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Les informations détaillées incluent :

- **DeletedSiteId** : Identificateur unique de l’application, utilisé pour les scénarios où plusieurs applications portant le même nom ont été supprimées.
- **SubscriptionID** : Abonnement contenant la ressource supprimée.
- **Emplacement** : Emplacement de l’application d’origine.
- **ResourceGroupName** : Nom du groupe de ressources d’origine.
- **Name** : Nom de l’application d’origine.
- **Emplacement** : Nom de l’emplacement.
- **Heure de la suppression** : Horodatage de la suppression de l’application.  

## <a name="restore-deleted-app"></a>Restaurer une application supprimée

Une fois que vous avez identifié l’application à restaurer, vous pouvez la restaurer à l’aide de `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Les entrées de la commande sont les suivantes :

- **Groupe de ressources** : Groupe de ressources cible dans lequel l’application sera restaurée.
- **Name** : Nom de l’application qui doit être globalement unique.
- **TargetAppServicePlanName** : Plan App Service lié à l’application.

Par défaut, `Restore-AzDeletedWebApp` restaure à la fois la configuration de votre application et son contenu. Si vous voulez uniquement restaurer le contenu, utilisez l’indicateur `-RestoreContentOnly` avec cette cmdlet.

> [!NOTE]
> Si l’application a été hébergée, puis supprimée d’un App Service Environment, elle ne peut être restaurée que si les App Service Environment correspondants existent toujours.
>

La référence complète de la cmdlet se trouve ici : [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
