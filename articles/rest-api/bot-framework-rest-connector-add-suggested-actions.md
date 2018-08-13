---
title: Hinzufügen vorgeschlagener Aktionen zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Nachrichten mithilfe des Bot Connector-Diensts empfohlene Aktionen hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: d162bdd3f34848b16380317c776f445bc4611157
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39303224"
---
# <a name="add-suggested-actions-to-messages"></a>Hinzufügen vorgeschlagener Aktionen zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

> [!TIP]
> Informationen dazu, wie die verschiedenen Kanäle vorgeschlagene Aktionen wiedergeben, finden Sie unter [Kanalprüfung][channelInspector].

## <a name="send-suggested-actions"></a>Senden vorgeschlagener Aktionen

Zum Hinzufügen vorgeschlagener Aktionen zu einer Nachricht legen Sie die `suggestedActions`-Eigenschaft der [Aktivität][Activity] auf die Liste der [CardAction][CardAction]-Objekte fest, die die Schaltflächen darstellen, die dem Benutzer angezeigt werden. 

Die folgende Anforderung sendet eine Nachricht mit drei vorgeschlagenen Aktionen für den Benutzer. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "text": "I have colors in mind, but need your help to choose the best one.",
    "inputHint": "expectingInput",
    "suggestedActions": {
        "actions": [
            {
                "type": "imBack",
                "title": "Blue",
                "value": "Blue"
            },
            {
                "type": "imBack",
                "title": "Red",
                "value": "Red"
            },
            {
                "type": "imBack",
                "title": "Green",
                "value": "Green"
            }
        ]
    },
    "replyToId": "5d5cdc723"
}
```

Wenn der Benutzer auf eine der vorgeschlagenen Aktionen tippt, empfängt der Bot vom Benutzer eine Nachricht mit dem `value` der entsprechenden Aktion.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)

[channelInspector]: ../bot-service-channel-inspector.md

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object

[CardAction]: bot-framework-rest-connector-api-reference.md#cardaction-object

[SuggestedAction]: bot-framework-rest-connector-api-reference.md#suggestedactions-object