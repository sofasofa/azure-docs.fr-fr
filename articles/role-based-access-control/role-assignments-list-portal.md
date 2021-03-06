---
title: Lister les attributions de rôles à l’aide du RBAC Azure et du portail Azure
description: Découvrez comment déterminer les ressources, utilisateurs, groupes, principaux de service ou identités managées qui ont accès au contrôle d’accès en fonction du rôle (RBAC) Azure et au portail Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 099cf74dd27f39a4289397d5178511125d9ebf6f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720706"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lister les attributions de rôles à l’aide du RBAC Azure et du portail Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Cet article explique comment lister les attributions de rôles à l’aide du portail Azure.

> [!NOTE]
> Si votre organisation possède des fonctions de gestion externalisées pour un fournisseur de services qui utilise la [gestion des ressources déléguées Azure](../lighthouse/concepts/azure-delegated-resource-management.md), les attributions de rôles autorisées par ce fournisseur de services ne seront pas affichées ici.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lister les attributions de rôles pour un utilisateur ou un groupe

La façon la plus simple de voir les rôles attribués à un utilisateur ou à un groupe dans un abonnement consiste à utiliser le volet **Ressources Azure**.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez **Utilisateurs** ou **Groupes**.

1. Cliquez sur l’utilisateur ou le groupe pour lequel vous voulez lister les attributions de rôles.

1. Cliquez sur **Ressources Azure**.

    Vous voyez une liste de rôles attribués à l’utilisateur ou au groupe sélectionné dans différentes étendues, comme un groupe d’administration, un abonnement, un groupe de ressources ou une ressource. Cette liste inclut toutes les attributions de rôles que vous êtes autorisé à lire.

    ![Attributions de rôles pour un utilisateur](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Pour changer d’abonnement, cliquez sur la liste **Abonnements**.

## <a name="list-owners-of-a-subscription"></a>Répertorier les propriétaires d’un abonnement

Les utilisateurs qui se sont vu attribuer le rôle [Propriétaire](built-in-roles.md#owner) pour un abonnement peuvent gérer tous les éléments de l’abonnement. Procédez comme suit pour répertorier les propriétaires d’un abonnement.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sur **Abonnements**.

1. Cliquez sur l’abonnement dont vous souhaitez répertorier les propriétaires.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cet abonnement.

1. Faites défiler la page jusqu’à la section **Propriétaires** pour voir tous les utilisateurs auxquels le rôle Propriétaire a été attribué pour cet abonnement.

   ![Contrôle d’accès à l’abonnement – Onglet Attributions de rôles](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Lister les attributions de rôles dans une étendue

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez l’étendue. Par exemple, vous pouvez sélectionner **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource.

1. Cliquez sur la ressource spécifique.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

   ![Contrôle d’accès - Onglet Attributions de rôles](./media/role-assignments-list-portal/access-control-role-assignments.png)

   L’onglet Attributions de rôles indique les personnes qui ont accès à cette étendue. Notez que certains rôles sont inclus dans l’étendue de **cette ressource**, tandis que d’autres sont **hérités** à partir d’une autre étendue. L’accès est attribué spécifiquement à cette ressource ou hérité d’une affectation à l’étendue parente.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lister les attributions de rôles pour un utilisateur dans une étendue

Pour lister l’accès pour un utilisateur, un groupe, un principal de service ou une identité managée, vous listez leurs attributions de rôles. Suivez ces étapes pour lister les attributions de rôles pour un utilisateur, un groupe, un principal de service ou une identité managée dans une étendue particulière.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis sélectionnez l’étendue. Par exemple, vous pouvez sélectionner **Groupes d’administration**, **Abonnements**, **Groupes de ressources**, ou une ressource.

1. Cliquez sur la ressource spécifique.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Vérifier l’accès**.

    ![Contrôle d’accès - onglet Vérifier l’accès](./media/role-assignments-list-portal/access-control-check-access.png)

1. Dans la liste **Rechercher**, sélectionnez le type principal de sécurité dont vous souhaitez contrôler l’accès.

1. Dans la zone de recherche, entrez une chaîne afin de rechercher, dans le répertoire, des noms d’affichage, des adresses e-mail ou des identificateurs d’objet.

    ![Liste de sélection Vérifier l’accès](./media/role-assignments-list-portal/check-access-select.png)

1. Cliquez sur le principal de sécurité pour ouvrir le volet **Affectations**.

    ![Volet Affectations](./media/role-assignments-list-portal/check-access-assignments.png)

    Dans ce volet, vous pouvez voir les rôles attribués au principal de sécurité sélectionné et la portée. S’il existe des affectations de refus dans cette étendue, ou héritées par cette étendue, elles sont listées.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Répertorier les attributions de rôles pour une identité gérée affectée par le système

1. Dans le Portail Azure, ouvrez une identité gérée affectée par le système.

1. Dans le menu de gauche, cliquez sur **identité**.

    ![Identité managée affectée par le système](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Sous **Attributions de rôle**, cliquez sur **Afficher les rôles RBAC Azure affectés à cette identité gérée**.

    Vous voyez une liste de rôles affectés à l’identité gérée affectée par le système sélectionnée à différentes étendues, telles que groupe d’administration, abonnement, groupe de ressources ou ressource. Cette liste inclut toutes les attributions de rôles que vous êtes autorisé à lire.

    ![Attributions de rôles pour une identité gérée affectée par le système](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Répertorier les attributions de rôles pour une identité gérée affectée par l’utilisateur

1. Dans le Portail Azure, ouvrez une identité gérée affectée par le système.

1. Cliquez sur **Ressources Azure**.

    Vous voyez une liste de rôles affectés à l’identité gérée affectée à l’utilisateur sélectionnée à différentes étendues, telles que groupe d’administration, abonnement, groupe de ressources ou ressource. Cette liste inclut toutes les attributions de rôles que vous êtes autorisé à lire.

    ![Attributions de rôles pour une identité gérée affectée par le système](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Pour changer d’abonnement, cliquez sur la liste **Abonnements**.

## <a name="list-number-of-role-assignments"></a>Répertorier le nombre d’attributions de rôles

Vous pouvez avoir jusqu’à **2 000** attributions de rôles dans chaque abonnement. Pour vous aider à suivre ces limites, l’onglet **Attributions de rôles** comprend un graphique qui répertorie le nombre actuel d’attributions de rôles.

![Contrôle d’accès : graphique du nombre d’attributions de rôles](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Si vous vous approchez du nombre maximal et que vous essayez d’ajouter d’autres attributions de rôles, un avertissement s’affiche dans le volet **Ajouter une attribution de rôle**. Vous pouvez réduire le nombre d’attributions de rôles en supprimant les attributions de rôles qui ne sont plus nécessaires ou en attribuant des rôles à des groupes plutôt qu’à des utilisateurs individuels.

![Contrôle d’accès : avertissement Ajouter une attribution de rôle](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et du portail Azure](role-assignments-portal.md)
- [Résoudre des problèmes liés au contrôle d'accès en fonction du rôle pour les ressources Azure](troubleshooting.md)
