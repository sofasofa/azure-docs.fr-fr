---
title: Métriques Azure Monitor pour Application Gateway
description: Découvrez comment utiliser les métriques pour surveiller les performances de la passerelle Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 695507b0d6997870dbea392e73e4f4470248d26c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985881"
---
# <a name="metrics-for-application-gateway"></a>Métriques pour Application Gateway

Application Gateway publie des points de données, appelés métriques, sur [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pour les performances de vos instances Application Gateway et principales. Ces métriques sont des valeurs numériques dans un ensemble ordonné de données chronologiques qui décrivent certains aspects de votre passerelle d’application à un moment donné. Si des requêtes transitent par Application Gateway, les métriques sont mesurées et envoyées par intervalles de 60 secondes. Si aucune requête ne passe par Application Gateway ou s’il n’y a aucune donnée pour une métrique, la métrique n’est pas signalée. Pour plus d’informations, voir [Mesures Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métriques prises en charge par le SKU Application Gateway v2

### <a name="timing-metrics"></a>Métriques de minutage

Application Gateway fournit plusieurs métriques de minutage intégrées associées à la requête et à la réponse qui sont toutes mesurées en millisecondes. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> S’il existe plusieurs écouteurs dans Application Gateway, filtrez toujours par dimension *Écouteur* tout en comparant différentes mesures de latence afin d’obtenir une inférence significative.

- **Temps de connexion au principal**

  Temps passé à établir une connexion avec l’application principale. 

  Cela comprend la latence du réseau, ainsi que le temps pris par la pile TCP du serveur principal pour établir de nouvelles connexions. Dans le cas de SSL, il comprend également le temps consacré à la négociation. 

- **Temps de réponse du premier octet du principal**

  Intervalle de temps entre le début de l’établissement d’une connexion au serveur principal et la réception du premier octet de l’en-tête de la réponse. 

  Cela correspond approximativement à la somme du *temps de connexion au principal*, du temps pris par la demande pour atteindre le principal depuis Application Gateway, du temps pris par l’application principale pour répondre (le temps nécessaire au serveur pour générer du contenu, extraire potentiellement des requêtes de base de données) et du temps mis par le premier octet de la réponse à atteindre Application Gateway à partir du principal.

- **Temps de réponse du dernier octet du principal**

  Intervalle de temps entre le début de l’établissement d’une connexion au serveur principal et la réception du dernier octet du corps de la réponse. 

  Cela correspond approximativement à la somme du *temps de réponse du premier octet du principal* et du temps de transfert des données (ce nombre peut varier considérablement en fonction de la taille des objets demandés et de la latence du réseau du serveur).

- **Durée totale d’Application Gateway**

  Temps moyen nécessaire pour la réception et le traitement d’une requête et l’envoi de la réponse. 

  Il s’agit de l’intervalle entre le moment où Application Gateway reçoit le premier octet de la requête HTTP et le moment où le dernier octet de la réponse a été envoyé au client. Cela comprend le temps de traitement pris par Application Gateway, le *temps de réponse du dernier octet du principal*, le temps pris par Application Gateway pour envoyer toutes les réponses et le *RTT client*.

- **RTT client**

  Durée moyenne des allers-retours entre les clients et Application Gateway.



Ces métriques peuvent être utilisées pour déterminer si le ralentissement observé est dû au réseau client, aux performances d’Application Gateway, à la saturation de la pile TCP du réseau principal et du serveur principal, aux performances de l’application principale ou à la taille volumineuse des fichiers.

Par exemple, s’il existe un pic dans la tendance *Temps de réponse du premier octet du principal*, mais que la tendance *Temps de connexion au principal* est stable, il peut être déduit que la latence de la passerelle d’application vers le principal et le temps nécessaire pour établir la connexion sont stables, et que le pic est dû à une augmentation du temps de réponse de l’application principale. En revanche, si le pic dans *Temps de réponse du premier octet du principal* est associé à un pic correspondant dans *Temps de connexion au principal*, il peut être déduit que le réseau entre Application Gateway et le serveur principal ou la pile TCP du serveur principal est saturé. 

Si vous remarquez un pic dans *Temps de réponse du dernier octet du principal*, mais que le *temps de réponse du premier octet du principal* est stable, il peut être déduit que le pic est dû à la demande d’un fichier plus volumineux.

De même, si la *durée totale de la passerelle d’application* présente un pic, mais que le *temps de réponse du dernier octet du principal* est stable, cela peut être le signe d’un goulot d’étranglement des performances au niveau d’Application Gateway ou d’un goulot d’étranglement dans le réseau entre le client et Application Gateway. En outre, si le *RTT client* présente également un pic correspondant, cela indique que la dégradation est due au réseau entre le client et le Application Gateway.

### <a name="application-gateway-metrics"></a>Mesures Application Gateway

Pour Application Gateway, les métriques suivantes sont disponibles :

- **Octets reçus**

   Nombre d’octets reçus par Application Gateway à partir des clients

- **Octets envoyés**

   Nombre d’octets envoyés par Application Gateway aux clients

- **Protocole TLS du client**

   Nombre de demandes TLS et non-TLS initiées par le client qui a établi la connexion avec Application Gateway. Pour afficher la distribution du protocole TLS, filtrez par la dimension Protocole TLS.

- **Unités de capacité actuelles**

   Nombre d’unités de capacité consommées. Les unités de capacité mesurent le coût basé sur la consommation qui est facturé en plus du coût fixe. Les unités de capacité incluent 3 déterminants : l’unité Compute, les connexions persistantes et le débit. Chaque unité de capacité est composée au maximum de ce qui suit : 1 unité Compute, 2 500 connexions permanentes ou 2,22 Mbits/s de débit.

- **Unités de calcul actuelles**

   Capacité processeur consommée. Les facteurs affectant l’unité Compute sont les connexions TLS/s, les calculs de réécriture d’URL et le traitement des règles WAF. 

- **Connexions en cours**

   Nombre total de connexions simultanées actives de clients à Application Gateway

- **Requêtes ayant échoué**

   Nombre de requêtes en échec servies par Application Gateway. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.


- **État de la réponse**

   État de la réponse HTTP retourné par Application Gateway. La distribution du code d’état de la réponse peut être ultérieurement classée par catégorie afin d’afficher les réponses dans les catégories 2xx, 3xx, 4xx et 5xx.

- **Débit**

   Nombre d’octets par seconde servis par Application Gateway

- **Total de requêtes**

   Nombre de requêtes réussies servies par Application Gateway. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.

- **Règles de correspondance du pare-feu d’applications web**

- **Règles déclenchées par le pare-feu d’applications web**

### <a name="backend-metrics"></a>Métriques du principal

Pour Application Gateway, les métriques suivantes sont disponibles :

- **État de la réponse du principal**

  Nombre de codes d’état de réponse HTTP retournés par les principaux. Cela n’inclut pas les codes de réponse générés par Application Gateway. La distribution du code d’état de la réponse peut être ultérieurement classée par catégorie afin d’afficher les réponses dans les catégories 2xx, 3xx, 4xx et 5xx.

- **Nombre d’hôtes intègres**

  Le nombre de serveurs principaux déterminés sains par la sonde d’intégrité. Vous pouvez filtrer sur une base de pool principal pour afficher le nombre d’hôtes sains dans un pool principal spécifique.

- **Nombre d’hôtes défectueux**

  Nombre de principaux déterminés défectueux par la sonde d’intégrité. Vous pouvez filtrer sur une base de pool principal pour afficher le nombre d’hôtes non sains dans un pool principal spécifique.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métriques prises en charge par le SKU Application Gateway v1

### <a name="application-gateway-metrics"></a>Mesures Application Gateway

Pour Application Gateway, les métriques suivantes sont disponibles :

- **Utilisation du processeur**

  Affiche l’utilisation des processeurs alloués à Application Gateway.  Dans des conditions normales, l’utilisation du processeur ne doit pas dépasser 90 %, car cela peut entraîner une latence dans les sites Web hébergés derrière Application Gateway et perturber l’expérience du client. Vous pouvez indirectement contrôler ou améliorer l'utilisation du processeur en modifiant la configuration d'Application Gateway en augmentant le nombre d'instances ou en passant à une taille SKU plus grande, ou en faisant les deux.

- **Connexions en cours**

  Nombre de connexions courantes établies avec Application Gateway

- **Requêtes ayant échoué**

  Nombre de requêtes en échec servies par Application Gateway. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.

- **État de la réponse**

  État de la réponse HTTP retourné par Application Gateway. La distribution du code d’état de la réponse peut être ultérieurement classée par catégorie afin d’afficher les réponses dans les catégories 2xx, 3xx, 4xx et 5xx.

- **Débit**

  Nombre d’octets par seconde servis par Application Gateway

- **Total de requêtes**

  Nombre de requêtes réussies servies par Application Gateway. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.

- **Règles de correspondance du pare-feu d’applications web**

- **Règles déclenchées par le pare-feu d’applications web**

### <a name="backend-metrics"></a>Métriques du principal

Pour Application Gateway, les métriques suivantes sont disponibles :

- **Nombre d’hôtes intègres**

  Le nombre de serveurs principaux déterminés sains par la sonde d’intégrité. Vous pouvez filtrer sur une base de pool principal pour afficher le nombre d’hôtes sains dans un pool principal spécifique.

- **Nombre d’hôtes défectueux**

  Nombre de principaux déterminés défectueux par la sonde d’intégrité. Vous pouvez filtrer sur une base de pool principal pour afficher le nombre d’hôtes non sains dans un pool principal spécifique.

## <a name="metrics-visualization"></a>Visualisation des métriques

Accédez à une passerelle d’application, sous **Supervision**, sélectionnez **Métriques**. Pour afficher les valeurs disponibles, sélectionnez la liste déroulante **MÉTRIQUE**.

Dans l’image suivante, consultez un exemple avec trois métriques affichées pour les 30 dernières minutes :

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pour afficher une liste actuelle des métriques, consultez [Mesures prises en charge avec Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Règles d’alerte sur les métriques

Vous pouvez démarrer des règles d’alerte en fonction des métriques d’une ressource. Par exemple, une alerte peut appeler un webhook ou envoyer un e-mail à un administrateur si le débit de la passerelle Application Gateway est au-dessus ou en dessous d’un seuil pour une période spécifiée.

L’exemple suivant vous guide dans la création d’une règle d’alerte qui envoie un e-mail à un administrateur lorsqu’un seuil de débit est dépassé :

1. Sélectionnez **Ajouter une alerte Métrique** pour ouvrir la page **Ajouter une règle**. Cette page est aussi accessible à partir de la page Métriques.

   ![Bouton Ajouter une alerte Métrique][6]

2. Dans la page **Ajouter une règle**, remplissez les sections Nom, Condition et Notifier, puis sélectionnez **OK**.

   * Dans le sélecteur **Condition**, sélectionnez une des quatre valeurs : **Supérieur à**, **Supérieur ou égal à**, **Inférieur à** ou **Inférieur ou égal à**.

   * Dans le sélecteur **Période**, sélectionnez une période allant de 5 minutes à 6 heures.

   * Si vous sélectionnez **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs** , l’e-mail peut être dynamiquement basé sur les utilisateurs qui ont accès à cette ressource. Dans le cas contraire, vous pouvez fournir une liste d’utilisateurs séparée par des virgules dans la zone **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)** .

   ![Page Ajouter une règle][7]

Si le seuil est dépassé, un e-mail similaire à celui de l’image suivante vous est envoyé :

![E-mail en cas de dépassement de seuil][8]

Une liste d’alertes apparaît une fois que vous avez créé une alerte Métrique. Elle fournit une vue d’ensemble de toutes les règles d’alerte.

![Liste d’alertes et de règles][9]

Pour en savoir plus sur les notifications d’alerte, consultez [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Pour en savoir plus sur les webhooks et sur la façon de les utiliser avec des alertes, consultez [Configurer un webhook sur une alerte de métrique Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Étapes suivantes

* Visualisez le compteur et les journaux des événements à l’aide des [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* Billet de blog sur la [visualisation de votre journal d’activité Azure avec Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
* Billet de blog sur [l’affichage et l’analyse des journaux d’activité Azure dans Power BI](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
