---
title: Hinzufügen vorgeschlagener Aktionen zu Nachrichten | Microsoft Docs
description: Erfahren Sie, wie Sie vorgeschlagene Aktionen mithilfe des Bot Builder SDK für JavaScript in Nachrichten senden können.
keywords: vorgeschlagene Aktionen, Schaltflächen, zusätzliche Eingaben
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 88186d3c6c925220fba099a5983c86b305f2dcae
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997099"
---
# <a name="add-suggested-actions-to-messages"></a>Hinzufügen vorgeschlagener Aktionen zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

[!include[Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)] 

## <a name="send-suggested-actions"></a>Senden vorgeschlagener Aktionen

Sie können eine Liste mit Vorschlägen für Aktionen (auch als „Schnellantworten“ bezeichnet) erstellen, die dem Benutzer für einen einzelnen Durchlauf der Unterhaltung angezeigt wird: 

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
