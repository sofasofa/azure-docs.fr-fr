---
title: Surveiller la connectivité des appareils à l’aide du IoT Central Explorer d’Azure
description: Surveillez les messages de l’appareil et observez les modifications de jumeau d’appareil via l’interface CLI d’IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3f18537b4b038844c9aa824593e354c23c792370
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026417"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Superviser la connectivité des appareils à l’aide d’Azure CLI

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Utilisez l’extension IoT d’Azure CLI pour consulter les messages que vos appareils envoient à IoT Central et observez les modifications apportées au jumeau d’appareil. Vous pouvez utiliser cet outil pour déboguer et observer la connectivité des appareils. Vous pouvez aussi diagnostiquer les problèmes des messages d’appareil n’atteignant pas le cloud ou les appareils qui ne répondent pas aux modifications du jumeau.

[Pour plus d’informations, consultez la référence sur les extensions d’Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Conditions préalables requises

+ Azure CLI installé et doté de la version 2.0.7 ou ultérieure. Vérifiez la version de votre Azure CLI en exécutant `az --version`. En savoir plus sur l’installation et la mise à jour à l’aide des [docs Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Un compte professionnel ou scolaire dans Azure, ajouté en tant qu’utilisateur dans une application IoT Central.

## <a name="install-the-iot-central-extension"></a>Installer l’extension IoT Central

Exécutez la commande suivante dans la console de ligne de commande pour installer :

```cmd/sh
az extension add --name azure-cli-iot-ext
```

Vérifiez la version de l’extension en exécutant 
```cmd/sh
az --version
```
Vous devez voir l’extension azure-cli-iot-ext doté de la version 0.8.1 ou d’une version ultérieure. Si ce n’est pas le cas, exécutez
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>En utilisant l’extension

Les sections suivantes décrivent les commandes et les options courantes que vous pouvez utiliser quand vous exécutez `az iot central`. Pour voir l’ensemble complet des commandes et des options, passez `--help` à `az iot central` ou à une de ses sous-commandes.

### <a name="login"></a>Connexion

Commencez par vous connecter à Azure CLI. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Obtenez l’ID d’application de votre application IoT Central
Dans **Administration/Paramètres de l’application**, copiez l’**ID d’application**. Vous l’utiliserez dans des étapes ultérieures.

### <a name="monitor-messages"></a>Surveiller les messages
Surveillez les messages envoyés à votre application IoT Central à partir de vos appareils. Cela inclut tous les en-têtes et toutes les annotations.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Voir les propriétés de l’appareil
Affichez les propriétés actuelles de lecture et de lecture/écriture pour un appareil donné.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment utiliser IoT Central Explorer, l’étape suivante conseillée consiste à explorer la [Gestion des appareils IoT Central](howto-manage-devices.md).
