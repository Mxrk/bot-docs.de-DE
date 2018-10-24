---
title: Hinzufügen vorgeschlagener Aktionen zu Nachrichten | Microsoft Docs
description: Erfahren Sie, wie Sie vorgeschlagene Aktionen mithilfe des Bot Builder SDK für JavaScript in Nachrichten senden können.
keywords: vorgeschlagene Aktionen, Schaltflächen, zusätzliche Eingaben
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/13/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1a42a127606ee72e16bd54eb507ecb4884996996
ms.sourcegitcommit: bd4f9669c0d26ac2a4be1ab8e508f163a1f465f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47430319"
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
