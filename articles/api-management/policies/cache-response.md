---
title: Exemple de la stratégie de gestion des API - Ajouter des fonctionnalités à un service back-end
titleSuffix: Azure API Management
description: Exemple de la stratégie de Gestion des API Azure - Montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442482"
---
# <a name="add-capabilities-to-a-backend-service"></a>Ajouter des fonctionnalités à un service principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

