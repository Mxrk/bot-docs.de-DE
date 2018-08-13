---
title: Senden von Nachrichten mit dem Bot Builder SDK | Microsoft Docs
description: Erfahren Sie, wie Nachrichten innerhalb des Bot Builder SDK gesendet werden.
keywords: Senden von Nachrichten, Nachrichtenaktivitäten, einfache Textnachricht, Sprache, gesprochene Nachricht
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 04/04/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b854eeb4ecb45a875ab3be5d867333cd63babffd
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302797"
---
# <a name="sending-messages"></a>Senden von Nachrichten

Die primäre Art und Weise, wie Ihr Bot mit Benutzern kommuniziert und seinerseits Kommunikation empfängt, erfolgt über **Nachrichten**aktivitäten. Einige Nachrichten bestehen einfach aus Nur-Text, während andere reichhaltigere Inhalte wie Karten oder Anlagen enthalten können. Der turn-Handler Ihres Bots empfängt Nachrichten vom Benutzer, und Sie können Antworten an den Benutzer von dort aus senden. Das turn-Kontextobjekt stellt Methoden zum Senden von Nachrichten zurück an den Benutzer bereit. Weitere Informationen zur Aktivitätsverarbeitung im Allgemeinen finden Sie unter [Aktivitätsverarbeitung](bot-builder-concept-activity-processing.md).

Dieser Artikel beschreibt, wie einfache Text- und Sprachnachrichten gesendet werden. Informationen zum Senden umfangreicherer Inhalte finden Sie unter [Hinzufügen von Rich-Media-Anlagen](bot-builder-howto-add-media-attachments.md). Informationen zum Verwenden von Eingabeaufforderungsobjekten finden Sie unter [Auffordern von Benutzern zur Eingabe](bot-builder-prompts.md).

## <a name="send-a-simple-text-message"></a>Senden einer einfachen Textnachricht

Um eine einfache Textnachricht zu senden, geben Sie die Zeichenfolge an, die Sie als Aktivität senden möchten:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Verwenden Sie in der Methode **OnTurn** des Bots die Methode **SendActivity** des turn-Kontextobjekts, um eine einzelne Nachrichtenantwort zu senden. Sie können auch die **SendActivities**-Methode des Objekts verwenden, um mehrere Antworten gleichzeitig zu senden.

```cs
await context.SendActivity("Greetings from sample message.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Verwenden Sie im turn-Handler des Bots die Methode **sendActivity** des turn-Kontextobjekts, um eine einzelne Nachrichtenantwort zu senden. Sie können auch die **sendActivities**-Methode des Objekts verwenden, um mehrere Antworten gleichzeitig zu senden.

```javascript
await context.sendActivity("Greetings from sample message.");
```

---

## <a name="send-a-spoken-message"></a>Senden einer gesprochenen Nachricht

Bestimmte Kanäle unterstützen sprachaktivierte Bots, sodass diese mit dem Benutzer sprechen können. Eine Nachricht kann sowohl über geschriebene als auch über gesprochene Inhalte verfügen.

> [!NOTE]
> Für die Kanäle, die Sprache nicht unterstützen, wird der Sprachinhalt ignoriert.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Verwenden Sie den optionalen **speak**-Parameter, um Text bereitzustellen, der als Teil der Antwort gesprochen wird.

```cs
await context.SendActivity(
    "This is the text to be displayed.",
    "This is the text to be spoken.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Um Sprache hinzuzufügen, benötigen Sie die `Microsoft.Bot.Builder.MessageFactory`, um die Nachricht erstellen zu können. `MessageFactory` wird häufiger mit [Rich-Media](bot-builder-howto-add-media-attachments.md) verwendet und in diesem Zusammenhang auch ausführlicher erläutert, aber im Moment wird sie hier einfach als erforderlich gekennzeichnet und verwendet.

```javascript
// Require MessageFactory from botbuilder
const {MessageFactory} = require('botbuilder');

const basicMessage = MessageFactory.text('This is the text that will be displayed.', 'This is the text that will be spoken.');
await context.sendActivity(basicMessage);
```

---