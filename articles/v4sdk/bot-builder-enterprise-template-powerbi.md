---
title: Konversationsanalyse mit Power BI | Microsoft-Dokumentation
description: Hier erfahren Sie, wie die Vorlage für den Bot für Unternehmen Application Insights nutzt, um über Power BI Erkenntnisse bereitzustellen.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 18dcaeaf2967a90ca3ecb8ff32c1ef6399d5f498
ms.sourcegitcommit: 3bf3dbb1a440b3d83e58499c6a2ac116fe04b2f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2018
ms.locfileid: "46708593"
---
# <a name="enterprise-bot-template---conversational-analytics-using-powerbi-dashboard-and-application-insights"></a>Vorlage für den Bot für Unternehmen: Konversationsanalyse mit Power BI-Dashboard und Application Insights

> [!NOTE]
> Dieses Thema gilt für die SDK-Version v4. 

Wenn Ihr Bot bereitgestellt wurde und mit der Verarbeitung von Nachrichten beginnt, gehen in der Application Insights-Instanz in Ihrer Ressourcengruppe Telemetriedaten ein. 

Diese Telemetriedaten können im Azure-Portal auf dem Application Insights-Blatt sowie mithilfe von Log Analytics angezeigt werden. Die gleichen Telemetriedaten können zudem von Power BI verwendet werden, um allgemeinere geschäftliche Erkenntnisse zur Verwendung Ihres Bots zu liefern.

Im Ordner „PowerBI“ des erstellten Projekts steht ein exemplarisches Power BI-Dashboard zur Verfügung. Dieses Dashboard wird zu Beispielzwecken bereitgestellt und erleichtert den Einstieg in die Generierung eigener Erkenntnisse. Die Visualisierungen werden im Laufe der Zeit weiter optimiert. 

## <a name="getting-started"></a>Erste Schritte

- [Laden Sie Power BI Desktop herunter.](https://powerbi.microsoft.com/en-us/desktop/)
 
- Rufen Sie eine ```Application Id``` für die von Ihrem Bot verwendete Application Insights-Ressource ab. Navigieren Sie hierzu zur API-Zugriffsseite des Konfigurationsabschnitts auf dem Azure-Blatt „Application Insights“.

Doppelklicken Sie im Ordner „PowerBI“ Ihrer Projektmappe auf die bereitgestellte Power BI-Vorlagendatei. Sie werden zur Angabe der ```Application Id``` aufgefordert, die Sie im vorherigen Schritt abgerufen haben. Schließen Sie die Authentifizierung ab, wenn Sie dazu aufgefordert werden. Verwenden Sie dabei die Anmeldeinformationen für Ihr Azure-Abonnement. Unter Umständen müssen Sie auf die Einstellung „Organisationskonto“ klicken, um sich anzumelden.

Das resultierende Dashboard ist jetzt mit Ihrer Application Insights-Instanz verknüpft, und es sollten erste Erkenntnisse angezeigt werden, sofern Nachrichten gesendet und empfangen wurden.

>Hinweis: Für die Stimmungsvisualisierung werden keine Daten angezeigt, da das aktuelle Bereitstellungsskript das Stimmungsfeature nicht verwendet, wenn das LUIS-Modell veröffentlicht wird. Wenn Sie das LUIS-Modell [erneut veröffentlichen](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-publish-app) und das Stimmungsfeature aktivieren, funktioniert es.

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
    - Conversationid
    - ConversationName
    - Locale
    - UserName
    - Text
```
  
```
-BotMessageSent
    - ActivityId,
    - Channel
    - RecipientId
    - Conversationid
    - ConversationName
    - Locale
    - ReceipientName
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
```

```
- QnAMaker
    - ActivityId
    - ConversationId
    - OriginalQuestion
    - UserName
    - QnAItemFound
    - Question
    - Answer
    - Score
```