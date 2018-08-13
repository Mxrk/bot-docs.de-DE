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
ms.openlocfilehash: 802d245112baa6d5fb10f4e992d9f6722b70a224
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303832"
---
# <a name="add-suggested-actions-to-messages"></a>Hinzufügen vorgeschlagener Aktionen zu Nachrichten

[!include[Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)] 

## <a name="send-suggested-actions"></a>Senden vorgeschlagener Aktionen

Sie können eine Liste mit Vorschlägen für Aktionen (auch als „Schnellantworten“ bezeichnet) erstellen, die dem Benutzer für einen einzelnen Durchlauf der Unterhaltung angezeigt wird:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Bot.Builder;
using Microsoft.Bot.Builder.Core.Extensions;
using Microsoft.Bot.Schema;

// Create the activity and add suggested actions.
var activity = MessageFactory.SuggestedActions(
    new CardAction[]
    {
        new CardAction(title: "red", type: ActionTypes.ImBack, value: "red"),
        new CardAction( title: "green", type: ActionTypes.ImBack, value: "green"),
        new CardAction(title: "blue", type: ActionTypes.ImBack, value: "blue")
    }, text: "Choose a color");

// Send the activity as a reply to the user.
await context.SendActivity(activity);
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Require MessageFactory from botbuilder.
const {MessageFactory} = require('botbuilder');

//  Initialize the message object.
const basicMessage = MessageFactory.suggestedActions(['red', 'green', 'blue'], 'Choose a color');

await context.sendActivity(basicMessage);
```

---

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Um eine Vorschau der Features anzuzeigen, können Sie die [Kanalprüfung](../bot-service-channel-inspector.md) verwenden.