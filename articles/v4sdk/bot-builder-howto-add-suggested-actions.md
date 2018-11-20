---
title: Verwenden von Schaltflächen für die Eingabe | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie vorgeschlagene Aktionen mithilfe des Bot Builder SDK für JavaScript in Nachrichten senden können.
keywords: vorgeschlagene Aktionen, Schaltflächen, zusätzliche Eingaben
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5e97bc4a991a9c9b27e9c14eb44f5fd1e230985f
ms.sourcegitcommit: cb0b70d7cf1081b08eaf1fddb69f7db3b95b1b09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51332786"
---
# <a name="use-button-for-input"></a>Verwenden von Schaltflächen für die Eingabe

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Sie können in Ihrem Bot Schaltflächen anzeigen, auf die der Benutzer tippen kann, um eine Eingabe bereitzustellen. Schaltflächen verbessern die Benutzerfreundlichkeit, da der Benutzer Fragen beantworten oder eine Auswahl treffen kann, indem er einfach auf eine Schaltfläche tippt, anstatt eine Antwort über eine Tastatur einzugeben. Im Gegensatz zu Schaltflächen in Rich Cards (die für den Benutzer auch nach dem Antippen sichtbar und zugänglich bleiben), verschwinden die im Bereich der vorgeschlagenen Aktionen angezeigten Schaltflächen, nachdem der Benutzer eine Auswahl getroffen hat. Dies verhindert, dass der Benutzer innerhalb einer Unterhaltung auf veraltete Schaltflächen tippen kann und vereinfacht die Botentwicklung (da Sie dieses Szenario nicht berücksichtigen müssen). 

## <a name="suggest-action-using-button"></a>Vorschlagen einer Aktion mithilfe von Schaltflächen

*Vorgeschlagene Aktionen* ermöglichen Ihrem Bot das Anzeigen von Schaltflächen. Sie können eine Liste mit Vorschlägen für Aktionen (auch als „Schnellantworten“ bezeichnet) erstellen, die dem Benutzer für einen einzelnen Durchlauf der Unterhaltung angezeigt wird: 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Sie finden den hier verwendeten Quellcode auf [GitHub](https://aka.ms/SuggestedActionsCSharp).

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

```csharp
var reply = turnContext.Activity.CreateReply("What is your favorite color?");

reply.SuggestedActions = new SuggestedActions()
{
    Actions = new List<CardAction>()
    {
        new CardAction() { Title = "Red", Type = ActionTypes.ImBack, Value = "Red" },
        new CardAction() { Title = "Yellow", Type = ActionTypes.ImBack, Value = "Yellow" },
        new CardAction() { Title = "Blue", Type = ActionTypes.ImBack, Value = "Blue" },
    },

};
await turnContext.SendActivityAsync(reply, cancellationToken);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Sie finden den hier verwendeten Quellcode auf [GitHub](https://aka.ms/SuggestActionsJS).

```javascript
const { ActivityTypes, MessageFactory, TurnContext } = require('botbuilder');

async sendSuggestedActions(turnContext) {
    var reply = MessageFactory.suggestedActions(['Red', 'Yellow', 'Blue'], 'What is the best color?');
    await turnContext.sendActivity(reply);
}
```

---

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Sie finden den hier angezeigten vollständigen Quellcode auf GitHub [[C#](https://aka.ms/SuggestedActionsCSharp) | [JS](https://aka.ms/SuggestActionsJS)].
