---
title: Configurer des clés gérées par le client pour votre compte Azure Cosmos DB
description: Découvrez comment configurer des clés gérées par le client pour votre compte Azure Cosmos DB avec Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 7a77a54dc59ec427bf6abdf8fc1d410533b5be44
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771898"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configurer des clés gérées par le client pour votre compte Azure Cosmos avec Azure Key Vault

> [!NOTE]
> À ce stade, vous devez demander l’accès pour utiliser cette fonctionnalité. Pour ce faire, veuillez contacter [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

Les données stockées dans votre compte Azure Cosmos sont chiffrées automatiquement et en toute transparence. Azure Cosmos DB offre deux options pour gérer les clés utilisées pour chiffrer les données au repos :

- **Clés gérées par le service** – Par défaut, Microsoft gère les clés utilisées pour chiffrer les données dans votre compte Azure Cosmos.

- **Clés gérées par le client (CMK)** – Vous pouvez éventuellement choisir d’ajouter une deuxième couche de chiffrement avec vos propres clés.

Vous devez stocker les clés gérées par le client dans le [coffre de clés Azure](../key-vault/key-vault-overview.md) et fournir une clé pour chaque compte Azure Cosmos activé avec les clés gérées par le client. Cette clé est utilisée pour chiffrer toutes les données stockées dans ce compte.

> [!NOTE]
> Actuellement, les clés gérées par le client sont uniquement disponibles pour les nouveaux comptes Azure Cosmos et vous devez les configurer lors de la création du compte.

## <a id="register-resource-provider"></a> Inscrire le fournisseur de ressources Azure Cosmos DB dans l’abonnement Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/), accédez à votre abonnement Azure et sélectionnez **Fournisseurs de ressources** sous l’onglet **Paramètres** :

   ![Entrée « Fournisseurs de ressources » dans le menu de gauche](./media/how-to-setup-cmk/portal-rp.png)

1. Recherchez le fournisseur de ressources **Microsoft.DocumentDB**. Vérifiez si le fournisseur de ressources est déjà marqué comme inscrit. Si ce n’est pas le cas, choisissez le fournisseur de ressources et sélectionnez **Inscrire** :

   ![Inscription du fournisseur de ressources Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configurer votre instance Azure Key Vault

L’utilisation de clés gérées par le client avec Azure Cosmos DB vous oblige à définir deux propriétés sur l’instance Azure Key Vault que vous prévoyez d’utiliser pour héberger vos clés de chiffrement. Ces propriétés incluent **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut, vous pouvez les activer à l’aide de PowerShell ou d’Azure CLI.

Pour savoir comment activer ces propriétés sur une instance Azure Key Vault existante, consultez les sections « Activation de la suppression réversible » et « Activation de la protection contre le vidage » dans l’un des articles suivants :

- [Guide pratique pour utiliser la suppression réversible avec Power​Shell](../key-vault/key-vault-soft-delete-powershell.md)
- [Guide pratique pour utiliser la suppression réversible avec Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Ajouter une stratégie d’accès à votre instance Azure Key Vault

1. Dans le Portail Azure, accédez à l’instance Azure Key Vault que vous prévoyez d’utiliser pour héberger vos clés de chiffrement. Sélectionnez **Stratégies d’accès** dans le menu de gauche :

   ![« Stratégies d’accès » dans le menu de gauche](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Sélectionnez **+ Ajouter une stratégie d’accès**.

1. Dans le menu déroulant **Autorisations de clé**, sélectionnez les autorisations **Obtenir**, **Ne pas inclure la clé** et **Inclure la clé** :

   ![Sélection des autorisations appropriées](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Sous **Sélectionner le principal**, sélectionnez **Aucune sélection**. Ensuite, recherchez le principal **Azure Cosmos DB** et sélectionnez-le. Enfin, cliquez sur **Sélectionner** en bas (si le principal **Azure Cosmos DB** ne figure pas dans la liste, vous devrez peut-être réinscrire le fournisseur de ressources **Microsoft.DocumentDB** comme cela est décrit dans la section [Inscrire le fournisseur de ressources](#register-resource-provider) de cet article) :

   ![Sélectionner le principal Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Sélectionnez **Ajouter** pour ajouter la nouvelle stratégie d’accès.

## <a name="generate-a-key-in-azure-key-vault"></a>Générer une clé dans Azure Key Vault

1. Dans le Portail Azure, accédez à l’instance Azure Key Vault que vous prévoyez d’utiliser pour héberger vos clés de chiffrement. Ensuite, sélectionnez **Clés** dans le menu de gauche :

   ![Entrée « Clés » dans le menu de gauche](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Sélectionnez **Générer/importer**, attribuez un nom à la nouvelle clé, sélectionnez une taille de clé RSA (un minimum de 3072 est recommandé pour une sécurité optimale), puis sélectionnez **Créer** :

   ![Créer une clé](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Une fois la clé créée, sélectionnez la clé que vous venez de créer puis sa version actuelle.

1. Copiez l’**identificateur de clé** de la clé à l’exception de la partie qui suit la dernière barre oblique :

   ![Copie de l’identificateur de clé de la clé](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Créer un compte Azure Cosmos

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Lorsque vous créez un compte Azure Cosmos DB à partir du Portail Azure, choisissez **Clé gérée par le client** dans l’étape **Chiffrement**. Dans le champ **URI de clé**, collez l’identificateur URI/clé de la clé Azure Key Vault que vous avez copiée à partir de l’étape précédente :

![Définition des paramètres de clé CMK dans le Portail Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Lors de la création d’un nouveau compte Azure Cosmos DB avec PowerShell,

- Transmettez l’URI de la clé Azure Key Vault copiée précédemment sous la propriété **keyVaultKeyUri** dans **PropertyObject**

- Utilisez **2019-12-12** comme version d’API.

> [!IMPORTANT]
> Vous devez définir explicitement le paramètre `Location` pour que le compte soit créé avec succès avec les clés gérées par le client.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Lors de la création d’un nouveau compte Azure Cosmos via un modèle Azure Resource Manager :

- Transmettez l’URI de la clé Azure Key Vault copiée précédemment sous la propriété **keyVaultKeyUri** dans l’objet **properties**.

- Utilisez **2019-12-12** comme version d’API.

> [!IMPORTANT]
> Vous devez définir explicitement le paramètre `Location` pour que le compte soit créé avec succès avec les clés gérées par le client.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Déployez le modèle avec le script PowerShell suivant :

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Des frais supplémentaires sont-ils facturés lors de l’utilisation de clés gérées par le client ?

Oui. Pour prendre en compte la charge de calcul supplémentaire requise pour gérer le chiffrement et le déchiffrement des données avec les clés gérées par le client, toutes les opérations exécutées sur le compte Azure Cosmos consomment 25 % d’[unités de demande](./request-units.md) en plus.

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quelles données sont-elles chiffrées avec les clés gérées par le client ?

Toutes les données stockées dans votre compte Azure Cosmos sont chiffrées avec les clés gérées par le client, à l’exception des métadonnées suivantes :

- Les noms de vos [comptes, bases de données et conteneurs](./account-overview.md#elements-in-an-azure-cosmos-account) Azure Cosmos DB

- Les noms de vos [procédures stockées](./stored-procedures-triggers-udfs.md)

- Les chemins de propriété déclarés dans vos [stratégies d’indexation](./index-policy.md)

- Les valeurs de la [clé de partition](./partitioning-overview.md) de vos conteneurs

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Les clés gérées par le client sont-elles prises en charge pour les comptes Azure Cosmos existants ?

Cette fonctionnalité n’est actuellement disponible que pour les nouveaux comptes.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existe-t-il un plan pour prendre en charge une plus grande précision que celle des clés au niveau du compte ?

Actuellement non, toutefois les clés au niveau du conteneur sont prises en compte.

### <a name="how-does-customer-managed-keys-affect-a-backup"></a>Comment les clés gérées par le client affectent-elles une sauvegarde ?

Azure Cosmos DB prend des [sauvegardes régulières et automatiques](./online-backup-and-restore.md) des données stockées dans votre compte. Cette opération sauvegarde les données chiffrées. Pour utiliser la sauvegarde restaurée, la clé de chiffrement que vous avez utilisée au moment de la sauvegarde est requise. Cela signifie qu’aucune révocation n’aura dû être effectuée et que la version de la clé qui a été utilisée au moment de la sauvegarde sera toujours activée.

### <a name="how-do-i-revoke-an-encryption-key"></a>Comment révoquer une clé de chiffrement ?

La révocation de la clé s’effectue en désactivant la dernière version de la clé :

![Désactiver la version d’une clé](./media/how-to-setup-cmk/portal-akv-rev2.png)

Sinon, pour révoquer toutes les clés d’une instance Azure Key Vault, vous pouvez supprimer la stratégie d’accès accordée au principal Azure Cosmos DB :

![Suppression de la stratégie d’accès pour le principal Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quelles opérations sont-elles disponibles après la révocation d’une clé gérée par le client ?

La seule opération possible lorsque la clé de chiffrement a été révoquée est la suppression du compte.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail le [chiffrement des données dans Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenez une vue d’ensemble de l’[accès sécurisé aux données dans Cosmos DB](secure-access-to-data.md).