---
title: Senden und Empfangen von SMS | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie im Bot Framework SDK SMS senden und empfangen.
keywords: Senden einer Nachricht, Nachrichtenaktivitäten, einfache SMS, Nachricht, SMS, Nachricht empfangen
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9cfe077c8d8573145625b211c3c1ca05a6a21e19
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224815"
---
# <a name="send-and-receive-text-message"></a>Senden und Empfangen von SMS 

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die primäre Art und Weise, wie Ihr Bot mit Benutzern kommuniziert und seinerseits Kommunikation empfängt, erfolgt über **Nachrichten**aktivitäten. Einige Nachrichten bestehen einfach aus Nur-Text, während andere reichhaltigere Inhalte wie Karten oder Anlagen enthalten können. Der turn-Handler Ihres Bots empfängt Nachrichten vom Benutzer, und Sie können Antworten an den Benutzer von dort aus senden. Das turn-Kontextobjekt stellt Methoden zum Senden von Nachrichten zurück an den Benutzer bereit. In diesem Artikel wird beschrieben, wie Sie einfache SMS senden.

## <a name="send-a-text-message"></a>Senden einer Textnachricht

Um eine einfache Textnachricht zu senden, geben Sie die Zeichenfolge an, die Sie als Aktivität senden möchten:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Verwenden Sie in der `OnTurnAsync`-Methode des Bots die `SendActivityAsync`-Methode des Turn-Kontextobjekts, um eine einzelne Nachrichtenantwort zu senden. Sie können auch die `SendActivitiesAsync`-Methode des Objekts verwenden, um mehrere Antworten gleichzeitig zu senden.

```cs
await turnContext.SendActivityAsync($"Welcome!");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Verwenden Sie im `onTurn`-Handler des Bots die `sendActivity`-Methode des Turn-Kontextobjekts, um eine einzelne Nachrichtenantwort zu senden. Sie können auch die `sendActivities`-Methode des Objekts verwenden, um mehrere Antworten gleichzeitig zu senden.

```javascript
await context.sendActivity("Welcome!");
```
---
## <a name="receive-a-text-message"></a>Empfangen einer Textnachricht

Verwenden Sie zum Empfangen einer einfachen SMS die *text*-Eigenschaft des *activity*-Objekts. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Verwenden Sie in der `OnTurnAsync`-Methode des Bots den folgenden Code, um eine Nachricht zu empfangen. 

```cs
var responseMessage = turnContext.Activity.Text;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Verwenden Sie in der `OnTurnAsync`-Methode des Bots den folgenden Code, um eine Nachricht zu empfangen. 
```javascript
let text = turnContext.activity.text;
```
---


## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen zur Aktivitätsverarbeitung im Allgemeinen finden Sie unter [Aktivitätsverarbeitung](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack). Informationen zum Senden umfangreicherer Inhalte finden Sie unter [Hinzufügen von Rich-Media-Anlagen](bot-builder-howto-add-media-attachments.md).
