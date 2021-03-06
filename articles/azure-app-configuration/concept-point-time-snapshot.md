---
title: Capture instantanée à un point dans le temps dans Azure App Configuration
description: Vue d’ensemble du fonctionnement d’une capture instantanée à un point dans le temps dans Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4a352ba913b6ad4e3c8607677078e21070f294fd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899593"
---
# <a name="point-in-time-snapshot"></a>Capture instantanée à un point dans le temps

Azure App Configuration conserve les enregistrements des dates de création et de modification précises des paires clé-valeur. Ces enregistrements forment une chronologie complète des modifications de paires clé-valeur. Un magasin App Configuration peut reconstruire l’historique d’une valeur de clé et relire son ancienne valeur à tout moment, jusqu’au moment présent. Avec cette fonctionnalité, vous pouvez « voyager dans le passé » pour récupérer une ancienne valeur de clé. Par exemple, vous pouvez obtenir les paramètres de configuration de la veille, juste avant le dernier déploiement en date, afin de récupérer une précédente configuration pour restaurer l’application.

## <a name="key-value-retrieval"></a>Récupération de paires clé-valeur

Pour récupérer d’anciennes valeurs de clé, spécifiez une heure à laquelle un instantané de ces valeurs de clé a été capturé dans l’en-tête HTTP d’un appel d’API REST. Par exemple :

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

Actuellement, App Configuration conserve sept jours d’historique de modifications.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET Core](./quickstart-aspnet-core-app.md)  
