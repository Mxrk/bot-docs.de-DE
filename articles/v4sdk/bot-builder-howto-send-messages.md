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
ms.date: 01/16/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ff52a62353df8983d94bbd09276de4ae94e6535e
ms.sourcegitcommit: c6ce4c42fc56ce1e12b45358d2c747fb77eb74e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453874"
---
# <a name="send-and-receive-text-message"></a>Senden und Empfangen von SMS

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Die primäre Art und Weise, wie Ihr Bot mit Benutzern kommuniziert und seinerseits Kommunikation empfängt, erfolgt über **Nachrichten**aktivitäten. Einige Nachrichten bestehen einfach aus Nur-Text, während andere reichhaltigere Inhalte wie Karten oder Anlagen enthalten können. Der turn-Handler Ihres Bots empfängt Nachrichten vom Benutzer, und Sie können Antworten an den Benutzer von dort aus senden. Das turn-Kontextobjekt stellt Methoden zum Senden von Nachrichten zurück an den Benutzer bereit. In diesem Artikel wird beschrieben, wie Sie einfache SMS senden.

Markdown wird für die meisten Textfelder unterstützt, die Unterstützung hängt jedoch ggf. vom jeweiligen Kanal ab.

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

- Weitere Informationen zur Aktivitätsverarbeitung im Allgemeinen finden Sie unter [Aktivitätsverarbeitung](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack).
- Informationen zum Senden umfangreicherer Inhalte finden Sie unter [Hinzufügen von Rich-Media-Anlagen](bot-builder-howto-add-media-attachments.md).
- Weitere Informationen zur Formatierung finden Sie im Bot Framework-Aktivitätsschema im Abschnitt zu [Nachrichtenaktivitäten](https://aka.ms/botSpecs-activitySchema#message-activity).
