---
title: Conseils de gestion des erreurs Azure Functions
description: Apprenez à gérer les erreurs dans Azure Functions avec des liens vers des erreurs de liaison spécifiques.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 06dc4c5c1713ee10f263e573a698e9ea36ca2662
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227323"
---
# <a name="azure-functions-error-handling"></a>Gestion d’erreurs d’Azure Functions

La gestion des erreurs dans Azure Functions est importante pour éviter des pertes de données, des événements manqués et pour superviser l’intégrité de votre application.

Cet article décrit les stratégies générales de gestion des erreurs, ainsi que des liens vers des erreurs propres à la liaison.

## <a name="handling-errors"></a>Gestion des erreurs

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Codes d’erreur de liaison

Lors de l’intégration aux services Azure, vous pouvez rencontrer des erreurs provenant des API des services sous-jacents. Les informations relatives aux erreurs propres à la liaison sont disponibles dans la section **Exceptions et codes de retour** des articles suivants :

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Stockage Blob](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hubs d'événements](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Stockage de files d’attente](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Stockage Table](functions-bindings-storage-table.md#exceptions-and-return-codes)
