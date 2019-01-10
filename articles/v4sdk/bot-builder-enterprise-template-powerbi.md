---
title: Konversationsanalyse mit Power BI | Microsoft-Dokumentation
description: Hier erfahren Sie, wie die Vorlage für den Bot für Unternehmen Application Insights nutzt, um über Power BI Erkenntnisse bereitzustellen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88208a2f5b0eb88d3b2964e63a21585484166d73
ms.sourcegitcommit: 2d84d5d290359ac3cfb8c8f977164f799666f1ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54152173"
---
# <a name="enterprise-bot-template---conversational-analytics-using-powerbi-dashboard-and-application-insights"></a>Vorlage für den Bot für Unternehmen: Konversationsanalyse mit Power BI-Dashboard und Application Insights

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

Wenn Ihr Bot bereitgestellt wurde und mit der Verarbeitung von Nachrichten beginnt, gehen in der Application Insights-Instanz in Ihrer Ressourcengruppe Telemetriedaten ein. 

Diese Telemetriedaten können im Azure-Portal auf dem Application Insights-Blatt sowie mithilfe von Log Analytics angezeigt werden. Die gleichen Telemetriedaten können zudem von Power BI verwendet werden, um allgemeinere geschäftliche Erkenntnisse zur Verwendung Ihres Bots zu liefern.

Ein PowerBI-Beispieldashboard finden Sie unter [Conversational AI Telemetry](https://aka.ms/botPowerBiTemplate) (Konversations-KI-Telemetriedaten). 

Dieses Dashboard wird zu Beispielzwecken bereitgestellt und erleichtert den Einstieg in die Generierung eigener Erkenntnisse. Die Visualisierungen werden im Laufe der Zeit weiter optimiert. 


## <a name="middleware-processing"></a>Middlewareverarbeitung

Telemetriedatenwrapper um die Klassen „QnAMaker“ und „LuisRecognizer“ werden bereitgestellt, um unabhängig vom Szenario eine konsistente Telemetriedatenausgabe zu gewährleisten und die Verwendung eines projektübergreifenden Standarddashboards zu ermöglichen.

```TelemetryLuisRecognizer``` und ```TelemetryQnAMaker``` bieten Eigenschaften für den Konstruktor, die es Entwicklern ermöglichen, die Protokollierung von Benutzernamen und ursprünglichen Nachrichten zu deaktivieren. Dadurch stehen jedoch weniger Erkenntnisse zur Verfügung.

## <a name="telemetry-captured"></a>Erfasste Telemetriedaten

Durch die Verwendung von ```TelemetryLuisRecognizer``` und ```TelemetryQnAMaker``` (in der Unternehmensvorlage standardmäßig aktiviert) werden vier individuelle Telemetrieereignisse erfasst. 

Jeder LUIS-Absicht, die von Ihrem Projekt verwendet wird, wird das Präfix „LuisIntent“ vorangestellt. Dadurch sind Absichten auf dem Dashboard sofort als solche erkennbar.

```
-BotMessageReceived
    - ActivityId
    - Channel
    - FromId
    - FromName
    - ConversationId
    - ConversationName
    - Locale
    - Text
    - RecipientId
    - RecipientName
```
  
```
-BotMessageSent
    - ActivityId,
    - Channel
    - RecipientId
    - ConversationId
    - ConversationName
    - Locale
    - RecipientId
    - RecipientName
    - ReplyToId
    - Text
```

```
- LuisIntent.*
    - ActivityId
    - Intent
    - IntentScore
    - SentimentLabel
    - SentimentScore
    - ConversationId
    - Question
    - DialogId
```

```
- QnAMaker
    - ActivityId
    - ConversationId
    - OriginalQuestion
    - FromName
    - ArticleFound
    - Question
    - Answer
    - Score
```