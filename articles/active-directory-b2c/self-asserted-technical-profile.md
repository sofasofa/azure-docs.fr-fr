---
title: Définir un profil technique autodéclaré dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définissez un profil technique autodéclaré dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6c70e1a5c7e5b81157c09a794ff75e276a20d1f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982736"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique autodéclaré dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Toutes les interactions dans Azure Active Directory B2C (Azure AD B2C) où une saisie de l’utilisateur est attendue sont des profils techniques autodéclarés. Par exemple, une page d’inscription, une page de connexion ou une page de réinitialisation du mot de passe.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom qualifié complet de l’assembly de gestionnaire de protocole utilisé par Azure AD B2C pour l’autodéclaration : `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

L’exemple suivant montre un profil technique autodéclaré pour l’inscription par e-mail :

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Revendications d’entrée

Dans un profil technique autodéclaré, vous pouvez utiliser les éléments **InputClaims** et **InputClaimsTransformations** pour prérenseigner la valeur des revendications qui apparaissent dans la page autodéclarée (revendications d’affichage). Par exemple, dans la stratégie de profil de modification, le parcours utilisateur lit d’abord le profil utilisateur à partir du service d’annuaire Azure AD B2C, puis le profil technique autodéclaré définit les revendications d’entrée avec les données utilisateur stockées dans le profil utilisateur. Ces revendications sont collectées à partir du profil utilisateur, puis présentées à l’utilisateur qui peut ensuite modifier les données existantes.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Revendications d’affichage

La fonctionnalité Revendications d’affichage est actuellement en **préversion**.

L’élément **DisplayClaims** contient une liste des revendications à présenter à l’écran pour recueillir des données de la part de l’utilisateur. Pour prérenseigner les valeurs des revendications de sortie, utilisez les revendications d’entrée décrites précédemment. L’élément peut également contenir une valeur par défaut.

L’ordre des revendications dans **DisplayClaims** détermine l’ordre dans lequel Azure AD B2C affiche les revendications à l’écran. Pour forcer l’utilisateur à fournir une valeur pour une revendication spécifique, affectez la valeur `true` à l’attribut **Required** de l’élément **DisplayClaims**.

L’élément **ClaimType** dans la collection **DisplayClaims** doit définir l’élément **UserInputType** sur n’importe quel type d’entrée d’utilisateur pris en charge par Azure AD B2C. Par exemple, `TextBox` ou `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Ajouter une référence à un DisplayControl

Dans la collection de revendications d’affichage, vous pouvez inclure une référence à un [DisplayControl](display-controls.md) que vous avez créé. Un contrôle d’affichage est un élément d’interface utilisateur qui présente une fonctionnalité particulière et interagit avec le service principal Azure AD B2C. Il permet à l’utilisateur d’exécuter des actions sur la page qui appellent un profil technique de validation dans le back end. Par exemple, vérifier une adresse e-mail, un numéro de téléphone ou un numéro de fidélité de client.

L’exemple suivant `TechnicalProfile` illustre l’utilisation de revendications d’affichage avec des contrôles d’affichage.

* La première revendication d’affichage fait référence au contrôle d’affichage `emailVerificationControl` qui collecte et vérifie l’adresse e-mail.
* La cinquième revendication d’affichage fait référence au contrôle d’affichage `phoneVerificationControl` qui collecte et vérifie un numéro de téléphone.
* Les autres revendications d’affichage sont des ClaimTypes à recueillir auprès de l’utilisateur.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Comme indiqué précédemment, une revendication d’affichage qui fait référence à un contrôle d’affichage peut exécuter sa propre validation, par exemple vérifier une adresse e-mail. De plus, la page auto-déclarée prend en charge un profil technique de validation pour valider l’ensemble de la page, notamment les entrées utilisateur (types de revendications ou contrôles d’affichage), avant de passer à l’étape d’orchestration suivante.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combiner soigneusement l’utilisation des revendications d’affichage et des revendications de sortie

Si vous spécifiez un ou plusieurs éléments **DisplayClaim** dans un profil technique auto-déclaré, vous devez utiliser un DisplayClaim pour *chaque* revendication que vous voulez afficher à l’écran et recueillir auprès de l’utilisateur. Un profil technique auto-déclaré qui contient au moins une revendication d’affichage ne présente aucune revendication de sortie.

Observez l’exemple suivant, dans lequel une revendication `age` est définie comme revendication de **sortie** dans une stratégie de base. Avant d’ajouter des revendications d’affichage au profil technique auto-déclaré, la revendication `age` est affichée à l’écran pour recueillir des données auprès de l’utilisateur :

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Si une stratégie de nœud terminal qui hérite de cette base spécifie par la suite `officeNumber` comme une revendication d’**affichage** :

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

La revendication `age` dans la stratégie de base n’est plus présentée à l’utilisateur à l’écran. Elle est effectivement « masquée ». Pour afficher la revendication `age` et recueillir la valeur d’âge auprès de l’utilisateur, vous devez ajouter un **DisplayClaim** `age`.

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications à renvoyer lors de la prochaine étape d’orchestration. L’attribut **DefaultValue** prend effet uniquement si la revendication n’a encore jamais été définie. Si elle a déjà été définie lors d’une étape d’orchestration précédente, la valeur par défaut ne prend pas effet même si l’utilisateur laisse la valeur vide. Pour forcer l’utilisation d’une valeur par défaut, affectez la valeur `true` à l’attribut **AlwaysUseDefaultValue**.

> [!NOTE]
> Dans les versions précédentes d’Identity Experience Framework (IEF), les revendications de sortie étaient utilisées pour recueillir des données auprès de l’utilisateur. Pour recueillir des données auprès de l’utilisateur, utilisez plutôt une collection **DisplayClaims**.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

### <a name="when-you-should-use-output-claims"></a>Cas d’utilisation des revendications de sortie

Dans un profil technique autodéclaré, la collection de revendications de sortie renvoie les revendications à l’étape d’orchestration suivante.

Vous devez utiliser des revendications de sortie dans les cas suivants :

- **Génération de revendications par le biais d’une transformation des revendications de sortie**.
- **Définition d’une valeur par défaut dans une revendication de sortie** sans recueillir de données de l’utilisateur ou retourner les données à partir du profil technique de validation. Le profil technique autodéclaré `LocalAccountSignUpWithLogonEmail` définit la revendication **executed-SelfAsserted-Input** sur `true`.
- **Un profil technique de validation retourne les revendications de sortie** : votre profil technique peut appeler un profil technique de validation qui retourne certaines revendications. Vous souhaiterez peut-être faire remonter les revendications et les retourner aux étapes d’orchestration suivantes dans le parcours utilisateur. Par exemple, quand vous vous connectez avec un compte local, le profil technique autodéclaré nommé `SelfAsserted-LocalAccountSignin-Email` appelle le profil technique de validation nommé `login-NonInteractive`. Ce profil technique valide les informations d’identification utilisateur et retourne également le profil utilisateur. Par exemple « userPrincipalName », « displayName », « givenName » et « surName ».
- **Un contrôle d’affichage renvoie les revendications de sortie** : votre profil technique peut comporter une référence à un [contrôle d’affichage](display-controls.md). Le contrôle d’affichage renvoie certaines revendications, telles que l’adresse e-mail vérifiée. Vous souhaiterez peut-être faire remonter les revendications et les retourner aux étapes d’orchestration suivantes dans le parcours utilisateur. La fonctionnalité Contrôle d’affichage est actuellement en **préversion**.

Dans l’exemple suivant, le profil technique autodéclaré utilise à la fois des revendications d’affichage et des revendications de sortie.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Conserver les revendications

Si l’élément **PersistedClaims** est absent, le profil technique autodéclaré ne conserve pas les données dans Azure AD B2C. Au lieu de cela, un appel est effectué à un profil technique de validation qui est responsable de la persistance des données. Par exemple, la stratégie d’inscription utilise le profil technique autodéclaré `LocalAccountSignUpWithLogonEmail` pour recueillir le nouveau profil utilisateur. Le profil technique `LocalAccountSignUpWithLogonEmail` appelle le profil technique de validation pour créer le compte dans Azure AD B2C.

## <a name="validation-technical-profiles"></a>Profils techniques de validation

Un profil technique de validation sert à valider toutes ou certaines des revendications de sortie du profil technique de référencement. Les revendications d’entrée du profil technique de validation doivent apparaître dans les revendications de sortie du profil technique autodéclaré. Le profil technique de validation valide l’entrée d’utilisateur et peut retourner une erreur à l’utilisateur.

Le profil technique de validation peut être n’importe quel profil technique dans la stratégie, tel qu’un profil technique [API REST](restful-technical-profile.md) ou [Azure Active Directory](active-directory-technical-profile.md). Dans l’exemple précédent, le profil technique `LocalAccountSignUpWithLogonEmail` valide le fait que le signinName n’existe pas dans l’annuaire. Si ce n’est pas le cas, le profil de validation technique crée un compte local et retourne les éléments objectId, authenticationSource et newUser. Le profil technique `SelfAsserted-LocalAccountSignin-Email` appelle le profil technique de validation `login-NonInteractive` pour valider les informations d’identification de l’utilisateur.

Vous pouvez également appeler un profil technique d’API REST avec votre logique métier, remplacer des revendications d’entrée ou enrichir les données utilisateur en fournissant une intégration approfondie à une application métier d’entreprise. Pour plus d’informations, consultez [Profil technique de Validation](validation-technical-profile.md)

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| setting.operatingMode | Non | Pour une page de connexion, cette propriété contrôle le comportement du champ username, tel que la validation de l’entrée et les messages d’erreur. Valeurs attendues : `Username` ou `Email`. |
| AllowGenerationOfClaimsWithNullValues| Non| Permet de générer une revendication avec une valeur Null. Par exemple, dans le cas où l’utilisateur ne sélectionne pas de case à cocher.|
| ContentDefinitionReferenceId | Oui | Identificateur de la [définition de contenu](contentdefinitions.md) associée à ce profil technique. |
| EnforceEmailVerification | Non | Pour l’inscription ou la modification du profil, applique la vérification par e-mail. Valeurs possibles : `true` (par défaut) ou `false`. |
| setting.retryLimit | Non | Contrôle le nombre de fois qu’un utilisateur peut essayer de fournir les données qui sont vérifiées par rapport à un profil technique de validation. Par exemple, un utilisateur tente de s’inscrire avec un compte qui existe déjà, et continue à essayer jusqu’à ce que la limite soit atteinte.
| SignUpTarget | Non | Identificateur d’échange de cible d’inscription. Quand l’utilisateur clique sur le bouton d’inscription, Azure AD B2C exécute l’identificateur d’échange spécifié. |
| setting.showCancelButton | Non | Affiche le bouton Annuler. Les valeurs possibles sont `true` (par défaut) ou `false` |
| setting.showContinueButton | Non | Affiche le bouton Continuer. Les valeurs possibles sont `true` (par défaut) ou `false` |
| setting.showSignupLink | Non | Affiche le bouton d’inscription. Les valeurs possibles sont `true` (par défaut) ou `false` |
| setting.forgotPasswordLinkLocation| Non| Affiche le lien du mot de passe oublié. Valeurs possibles : `AfterInput` (par défaut) où le lien est affiché en bas de la page ou `None` supprime le lien du mot de passe oublié.| 
## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** n’est pas utilisé.
