---
title: Licences requises pour utiliser Privileged Identity Management – Azure Active Directory | Microsoft Docs
description: Décrit les licences requises pour utiliser Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932316"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licences requises pour utiliser Privileged Identity Management

Pour utiliser Azure Active Directory (Azure AD) Privileged Identity Management (PIM), votre annuaire doit avoir une licence valide. De plus, les licences doivent être affectées aux administrateurs et aux utilisateurs concernés. Cet article décrit les licences requises pour utiliser Privileged Identity Management.

## <a name="valid-licenses"></a>Licences valides

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>De combien de licences avez-vous besoin ?

Assurez-vous que votre répertoire comporte au moins autant de licences Azure AD Premium P2 que d’employés effectuant les tâches suivantes :

- Utilisateurs désignés comme étant éligibles pour les rôles Azure Active Directory gérés à l’aide de PIM
- Utilisateurs en mesure d’approuver ou de refuser des demandes d’activation dans PIM
- Utilisateurs affectés à un rôle de ressource Azure avec des attributions juste-à-temps ou directes (à durée définie)  
- Utilisateurs affectés à une révision d’accès
- Utilisateurs qui effectuent des révisions d’accès

Les licences Azure AD Premium P2 ne sont **pas** requises pour les tâches suivantes :

- Aucune licence n’est requise pour les utilisateurs disposant des rôles d’administrateur général ou d’administrateur de rôle privilégié qui configurent PIM, des stratégies et les révisions d’accès et reçoivent des alertes.

Pour plus d’informations sur les licences, consultez [Assigner ou supprimer des licences à l’aide du portail Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Exemples de scénarios de licence

Voici quelques exemples de scénarios de licence pour vous aider à déterminer le nombre de licences dont vous devez disposer.

| Scénario | Calcul | Nombre de licences |
| --- | --- | --- |
| Woodgrove Bank a 10 administrateurs dans différents services et 2 administrateurs généraux qui configurent et gèrent PIM. Cinq administrateurs sont admissibles. | Cinq licences pour les administrateurs admissibles | 5 |
| Graphic Design Institute a 25 administrateurs dont 14 sont gérés via PIM. L’activation de rôle nécessite une approbation et trois utilisateurs différents dans l’organisation peuvent approuver les activations. | 14 licences pour les rôles admissibles + 3 approbateurs | 17 |
| Contoso a 50 administrateurs dont 42 sont gérés via PIM. L’activation de rôle nécessite une approbation et cinq utilisateurs différents dans l’organisation peuvent approuver les activations. Contoso effectue également des révisions mensuelles des utilisateurs affectés aux rôles Administrateur et les réviseurs sont les gestionnaires des utilisateurs dont six ne sont pas des rôles Administrateur gérés par PIM. | 42 licences pour les rôles admissibles + 5 approbateurs + 6 réviseurs | 53 |

## <a name="what-happens-when-a-license-expires"></a>Que se passe-t-il quand une licence expire ?

Si une licence Azure AD Premium P2, une licence EMS E5 ou une licence d’essai expire, les fonctionnalités Privileged Identity Management ne sont plus disponibles dans votre annuaire :

- Les affectations de rôle permanentes à des rôles Azure AD sont annulées.
- Le service Privileged Identity Management sur le Portail Azure, ainsi que les cmdlets API Graph et les interfaces PowerShell de Privileged Identity Management ne permettent plus aux utilisateurs d’activer des rôles privilégiés, de gérer les accès privilégiés ou de procéder à des révisions d’accès des rôles privilégiés.
- Les affectations de rôle éligibles de rôles Azure AD sont supprimées, car les utilisateurs ne sont plus en mesure d’activer des rôles privilégiés.
- Les révisions d’accès des rôles Azure AD en cours touchent à leur fin et les paramètres de configuration Privileged Identity Management sont supprimés.
- Privileged Identity Management n’envoie plus d’e-mails concernant les modifications apportées aux attributions de rôles.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer Privileged Identity Management](pim-deployment-plan.md)
- [Commencer à utiliser Privileged Identity Management](pim-getting-started.md)
- [Rôles non gérables dans Privileged Identity Management](pim-roles.md)
