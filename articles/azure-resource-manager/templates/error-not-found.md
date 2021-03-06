---
title: Erreurs liées à des ressources introuvables
description: Explique comment résoudre les erreurs liées à des ressources introuvables lors d'un déploiement à l'aide d'un modèle Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773263"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Résoudre les erreurs de ressources Azure introuvables

Cet article décrit les erreurs que vous pouvez observer lorsqu’une ressource est introuvable pendant le déploiement.

## <a name="symptom"></a>Symptôme

Lorsque votre modèle inclut le nom d’une ressource qui ne peut pas être résolue, vous recevez une erreur similaire à celle-ci :

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si vous utilisez les fonctions [reference](template-functions-resource.md#reference) ou [listKeys](template-functions-resource.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez l’erreur suivante :

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Cause :

Resource Manager a besoin de récupérer les propriétés d’une ressource, mais ne peut pas identifier la ressource dans votre abonnement.

## <a name="solution-1---set-dependencies"></a>Solution 1 : Définir des dépendances

Si vous souhaitez déployer la ressource manquante dans le modèle, vérifiez si vous devez ajouter une dépendance. Resource Manager optimise le déploiement en créant des ressources en parallèle, quand cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** de votre modèle. Par exemple, quand vous déployez une application web, le plan App Service doit être présent. Si vous n’avez pas spécifié que l’application web est dépendante du plan App Service, Resource Manager crée les deux ressources en même temps. Vous obtenez une erreur indiquant que la ressource du plan App Service est introuvable, car elle n’existe pas encore au moment où vous tentez de définir une propriété dans l’application web. Vous pouvez éviter cette erreur en définissant la dépendance dans l’application web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Il vaut mieux éviter de définir des dépendances qui ne sont pas nécessaires. Lorsque vous avez des dépendances inutiles, vous prolongez la durée du déploiement en empêchant les ressources qui ne sont pas interdépendantes d’être déployées en parallèle. Par ailleurs, il se peut que créiez des dépendances circulaires qui bloquent le déploiement. La fonction [reference](template-functions-resource.md#reference) et les fonctions [list*](template-functions-resource.md#list) créent une dépendance implicite envers la ressource référencée, lorsque cette ressource est déployée dans le même modèle et référencée par son nom (et non l’ID de la ressource). Il se peut donc que vous ayez des dépendances en plus des dépendances spécifiées dans la propriété **dependsOn**. La fonction [resourceId](template-functions-resource.md#resourceid) ne crée pas de dépendance implicite pas plus qu’elle ne valide l’existence de la ressource. La fonction [reference](template-functions-resource.md#reference) et les fonctions [list*](template-functions-resource.md#list) ne créent pas de dépendance implicite lorsque la ressource est référencée par son ID. Pour créer une dépendance implicite, transmettez le nom de la ressource qui est déployée dans le même modèle.

Si vous observez des problèmes de dépendance, vous devez déterminer l’ordre de déploiement des ressources. Pour afficher l’ordre des opérations de déploiement :

1. Sélectionnez l’historique de déploiement de votre groupe de ressources.

   ![sélectionner l’historique de déploiement](./media/error-not-found/select-deployment.png)

2. Sélectionnez un déploiement dans l’historique, puis sélectionnez **Événements**.

   ![sélectionner les événements de déploiement](./media/error-not-found/select-deployment-events.png)

3. Examinez la séquence d’événements de chaque ressource. Faites attention à l’état de chaque opération. Par exemple, l’image suivante montre trois comptes de stockage déployés en parallèle. Remarquez que les trois comptes de stockage sont démarrés en même temps.

   ![déploiement en parallèle](./media/error-not-found/deployment-events-parallel.png)

   L’image suivante montre trois comptes de stockage non déployés en parallèle. Le deuxième compte de stockage dépend du premier compte de stockage, et le troisième du deuxième. Le premier compte de stockage est démarré, accepté et terminé avant que le suivant ne démarre.

   ![déploiement séquentiel](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solution 2 : Obtenir les ressources des différents groupes de ressources

Lorsque la ressource existe dans un groupe de ressources différent de celui dans lequel a lieu le déploiement, utilisez la [fonction resourceId](template-functions-resource.md#resourceid) pour obtenir le nom complet de la ressource.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solution 3 : Vérifier la fonction de référence

Recherchez une expression qui inclut la fonction [reference](template-functions-resource.md#reference). Les valeurs que vous fournissez varient selon si la ressource fait partie des mêmes modèle, groupe de ressources et abonnement. Vérifiez que vous fournissez les valeurs de paramètres requises pour votre scénario. Si la ressource se trouve dans un autre groupe de ressources, fournissez l’ID complet de la ressource. Par exemple, pour faire référence à un compte de stockage dans un autre groupe de ressources, utilisez :

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Solution 4 : Obtenir une identité managée à partir d'une ressource

Si vous déployez une ressource qui crée implicitement une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md), vous devez attendre que cette ressource soit déployée avant de récupérer des valeurs sur l'identité managée. Si vous transmettez le nom de l'identité managée à la fonction [reference](template-functions-resource.md#reference), Resource Manager tente de résoudre la référence avant que la ressource et l'identité ne soient déployées. Transmettez plutôt le nom de la ressource à laquelle l'identité est appliquée. Cette approche garantit que la ressource et l'identité gérée seront déployées avant que Resource Manager ne résolve la fonction reference.

Dans la fonction reference, utilisez `Full` pour obtenir toutes les propriétés, y compris l'identité managée.

Par exemple, pour obtenir l'ID de locataire d'une identité managée appliquée à un groupe de machines virtuelles identiques, utilisez :

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```