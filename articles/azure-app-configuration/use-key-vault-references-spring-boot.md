---
title: Tutoriel sur l’utilisation de références Key Vault avec Azure App Configuration dans une application Java Spring Boot | Microsoft Docs
description: Dans ce tutoriel, vous apprenez à utiliser les références Key Vault avec Azure App Configuration depuis une application Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 17d86f25de6eecee535d6f812f4ef0b078a4b6db
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752495"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Tutoriel : Utiliser des références Key Vault dans une application Java Spring

Dans ce tutoriel, vous apprenez à utiliser le service Azure App Configuration avec Azure Key Vault. App Configuration et Key Vault sont des services complémentaires utilisés côte à côte dans la plupart des déploiements d’applications.

App Configuration vous aide à utiliser ces services ensemble en créant des clés qui référencent des valeurs stockées dans Key Vault. Lorsque le service App Configuration crée de telles clés, il stocke les URI des valeurs Key Vault plutôt que les valeurs elles-mêmes.

Votre application utilise le fournisseur client de App Configuration pour récupérer les références Key Vault, tout comme pour toutes les autres clés stockées dans App Configuration. Dans ce cas, les valeurs stockées dans App Configuration sont des URI qui référencent les valeurs dans le coffre de clés. Elles ne sont pas des valeurs de coffre de clés ou des informations d’identification. Étant donné que le fournisseur client reconnaît les clés comme des références Key Vault, il utilise Key Vault pour récupérer leurs valeurs.

Votre application est chargée de s’authentifier correctement auprès d’App Configuration et auprès de Key Vault. Les deux services ne communiquent pas directement.

Ce tutoriel vous montre comment implémenter des références Key Vault dans votre code. Il s’appuie sur l’application web introduite dans les guides de démarrage rapide. Avant de continuer, suivez d’abord [Créer une application Java Spring avec App Configuration](./quickstart-java-spring-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. Par exemple, [Visual Studio Code](https://code.visualstudio.com/) est un éditeur de code multiplateforme qui est disponible pour les systèmes d’exploitation Windows, macOS et Linux.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une clé App Configuration qui référence une valeur stockée dans Key Vault.
> * Accéder à la valeur de cette clé à partir d’une application Java Spring.

## <a name="prerequisites"></a>Conditions préalables requises

Avant d’effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Création d'un coffre

1. Sélectionnez l’option **Créer une ressource** dans le coin supérieur gauche du Portail Azure :

    ![La sortie après la création du coffre de clés](./media/quickstarts/search-services.png)
1. Dans la zone de recherche, entrez **Key Vault**.
1. Dans la liste des résultats, sélectionnez **Coffres de clés** sur la gauche.
1. Dans **Coffres de clés**, sélectionnez **Ajouter**.
1. Sur la droite, dans **Créer un coffre de clés**, renseignez les informations suivantes :
    - Sélectionnez **Abonnement** pour choisir un abonnement.
    - Dans **Groupe de ressources**, sélectionnez **Créer un nouveau** et entrez un nom de groupe de ressources.
    - Dans **Nom du coffre de clés**, un nom unique est requis. Pour ce tutoriel, entrez **Contoso-vault2**.
    - Dans la liste déroulante **Région**, choisissez un emplacement.
1. Conservez les valeurs par défaut des autres options **Création d’un coffre de clés**.
1. Sélectionnez **Create** (Créer).

À ce stade, votre compte Azure est le seul autorisé à accéder à ce nouveau coffre.

![La sortie après la création du coffre de clés](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés

Pour ajouter un secret au coffre, vous n’avez qu’à effectuer deux autres étapes. Dans ce cas, ajoutez un message que vous pouvez utiliser pour tester la récupération de Key Vault. Le message est appelé **Message** et vous stockez la valeur « Hello from Key Vault » dedans.

1. Depuis les pages des propriétés Key Vault, sélectionnez **Secrets**.
1. Sélectionnez **Générer/Importer**.
1. Dans le volet **Créer un secret**, saisissez les valeurs suivantes :
    - **Options de chargement** : Entrez **Manuel**.
    - **Name** : Entrez **Message**.
    - **Valeur** : Entrez **Hello from Key Vault**.
1. Conservez les valeurs par défaut des autres propriétés **Créer un secret**.
1. Sélectionnez **Create** (Créer).

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Ajouter une référence Key Vault à App Configuration

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance du magasin App Configuration que vous avez créée dans le guide de démarrage rapide.

1. Sélectionnez **Explorateur de configuration**.

1. Sélectionnez **+ Créer** > **Référence Key Vault**, puis choisissez les valeurs suivantes :
    - **Clé** : Sélectionnez **/application/config.keyvaultmessage**
    - **Étiquette** : Laissez cette valeur vide.
    - **Abonnement**, **Groupe de ressources** et **Key Vault** : Entrez les valeurs correspondant aux valeurs du coffre de clés que vous avez créé à la section précédente.
    - **Secret** : Sélectionnez le secret nommé **Message** que vous avez créé dans la section précédente.

## <a name="connect-to-key-vault"></a>Se connecter à Key Vault

1. Dans ce tutoriel, vous utilisez un principal de service pour l’authentification auprès de KeyVault. Pour créer ce principal de service, utilisez la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Cette opération retourne une série de paires clé/valeur :

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Exécutez la commande suivante pour autoriser le principal du service à accéder à votre coffre de clés :

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Créez les variables d’environnement suivantes, en utilisant les valeurs du principal de service affichées à l’étape précédente :

    * **AZURE_CLIENT_ID** : *clientId*
    * **AZURE_CLIENT_SECRET** : *clientSecret*
    * **AZURE_TENANT_ID** : *tenantId*

> [!NOTE]
> Ces informations d’identification Key Vault sont utilisées uniquement dans votre application. Votre application s’authentifie directement auprès de Key Vault à l’aide de ces informations d’identification. Elles ne sont jamais transmises au service App Configuration.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Mettre à jour votre code pour utiliser une référence Key Vault

1. Ouvrez *MessageProperties.java*. Ajoutez une nouvelle variable appelée *keyVaultMessage* :

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Ouvrez *HelloController.java*. Mettez à jour la méthode *getMessage* pour inclure le message récupéré à partir de Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Générez votre application Spring Boot avec Maven, puis exécutez-la. Par exemple :

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
1. Lorsque votre application s’exécute, utilisez *curl* pour la tester. Par exemple :

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Vous voyez le message que vous avez entré dans le magasin App Configuration. Vous voyez également le message que vous avez entré dans Key Vault.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous créez une clé App Configuration qui référence une valeur stockée dans Key Vault. Pour savoir comment utiliser les indicateurs de fonctionnalités dans votre application Java Spring, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./quickstart-feature-flag-spring-boot.md)
