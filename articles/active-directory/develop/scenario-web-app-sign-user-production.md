---
title: Passer en production une application web connectant des utilisateurs - Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application web qui connecte des utilisateurs (passage en production)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768101"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Application web qui connecte les utilisateurs : Passer en production

Maintenant que vous savez comment obtenir un jeton pour appeler des API web, découvrez comment passer en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

### <a name="same-site"></a>Même site

Vérifier que vous comprenez les problèmes susceptibles d’affecter les nouvelles versions du navigateur Chrome

> [!div class="nextstepaction"]
> [Guide pratique pour gérer les changements de cookie SameSite dans le navigateur Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scénario d’appel d’API web

Lorsque votre application web connecte des utilisateurs, elle peut appeler des API web pour le compte des utilisateurs connectés. L’appel d’API web à partir de l’application web est l’objet du scénario suivant :

> [!div class="nextstepaction"]
> [Application web qui appelle des API web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Présentation approfondie : Didacticiel sur les applications web ASP.NET Core

En savoir plus sur les autres façons de connecter les utilisateurs avec ce didacticiel ASP.NET Core : 

> [!div class="nextstepaction"]
> [Permettez à vos applications web de connecter des utilisateurs et d’appeler des API avec la plateforme d’identité Microsoft pour les développeurs](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ce didacticiel progressif propose un code prêt pour la production d’une application web, avec la procédure d’ajout de connexion aux comptes dans :

- votre organisation
- plusieurs organisations
- des comptes professionnels et scolaires ou des comptes Microsoft personnels
- [Azure AD B2C](https://aka.ms/aadb2c)
- Clouds nationaux

## <a name="sample-code-java-web-app"></a>Exemple de code : Applications web Java

En savoir plus sur l’application web Java à partir de cet exemple sur GitHub : 

> [!div class="nextstepaction"]
> [Une application web Java qui connecte les utilisateurs à la plateforme d’identités Microsoft et appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
