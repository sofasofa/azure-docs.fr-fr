---
title: Clouds souverains – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les clouds souverains
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: 3b96ded52aa2148df9e96d6f1e878d50b821abab
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170455"
---
# <a name="speech-services-with-sovereign-clouds"></a>Services Speech avec les clouds souverains

## <a name="azure-government-united-states"></a>Azure Government (États-Unis)

Seules les instances gouvernementales des États-Unis de niveau fédéral, étatique, local et tribal ainsi que leurs partenaires ont accès à cette instance dédiée, dont les opérations sont contrôlées par des citoyens américains sélectionnés.
- Régions : Gouvernement américain - Virginie
- Reconnaissance vocale dans SpeechSDK :*config.FromHost("wss://virginia.stt.speech.azure.us", "\<your-key\>");*
- Conversion de texte par synthèse vocale dans SpeechSDK : *config.FromHost("https[]()://virginia.tts.speech.azure.us", "\<your-key\>");*
- Jetons d’authentification : https[]()://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Portail Azure : https://portal.azure.us  
- Portail Custom Speech : https://virginia.cris.azure.us/Home/CustomSpeech
- Références SKU disponibles : S0
- Fonctionnalités prises en charge :
  - Reconnaissance vocale
  - Custom Speech (adaptation acoustique/linguistique)
  - Synthèse vocale
  - Traduction vocale
- Fonctionnalités non prises en charge
  - Custom Voice
  - Voix neurales pour la conversion de texte par synthèse vocale
- Paramètres régionaux pris en charge : les langues dont les paramètres régionaux sont pris en charge sont les suivantes.
  - Arabe (ar-*)
  - Chinois (zh-*)
  - Anglais (en-*)
  - Français (fr-*)
  - Allemand (de-*)
  - Hindi
  - Coréen
  - Russe
  - Espagnol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Chine

Centre de données Azure situé en Chine avec un accès direct à China Mobile, China Telecom, China Unicom et d’autres réseaux d’infrastructure de grands opérateurs pour permettre aux utilisateurs chinois d’accéder aux réseaux locaux de façon stable et rapide.
- Régions : Chine Est 2 (Shanghai)
- Reconnaissance vocale dans SpeechSDK :*config.FromHost("wss://chinaeast2.stt.speech.azure.cn", "\<your-key\>");*
- Conversion de texte par synthèse vocale dans SpeechSDK :  *config.FromHost("https[]()://chinaeast2.tts.speech.azure.cn", "\<your-key\>");*
- Jetons d’authentification : https[]()://chinaeast2.api.cognitive.microsoft.cn/sts/v1.0/issueToken
- Portail Azure : https://portal.azure.cn
- Portail Custom Speech : https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Références SKU disponibles : S0
- Fonctionnalités prises en charge :
  - Reconnaissance vocale
  - Custom Speech (adaptation acoustique/linguistique)
  - Synthèse vocale
  - Traduction vocale
- Fonctionnalités non prises en charge
  - Custom Voice
  - Voix neurales pour la conversion de texte par synthèse vocale
- Paramètres régionaux pris en charge : les langues dont les paramètres régionaux sont pris en charge sont les suivantes.
  - Arabe (ar-*)
  - Chinois (zh-*)
  - Anglais (en-*)
  - Français (fr-*)
  - Allemand (de-*)
  - Hindi
  - Coréen
  - Russe
  - Espagnol (es-*)

