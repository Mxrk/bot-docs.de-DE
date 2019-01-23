---
title: Hinzufügen vorgeschlagener Aktionen zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe des Bot Framework SDK für Node.js vorgeschlagene Aktionen in Nachrichten senden können.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 06/06/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 975496786e66a4d9b1de5c6ead6d8257687f23b7
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224525"
---
# <a name="add-suggested-actions-to-messages"></a>Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

> [!TIP]
> Verwenden Sie den [Kanalinspektor][channelInspector], wenn Sie sehen möchten, wie vorgeschlagene Aktionen auf verschiedenen Kanälen aussehen und funktionieren.

## <a name="suggested-actions-example"></a>Beispiel für vorgeschlagene Aktionen

Zum Hinzufügen vorgeschlagener Aktionen zu einer Nachricht legen Sie die `suggestedActions`-Eigenschaft der Nachricht auf eine Liste mit [Kartenaktionen][ICardAction] fest, die die Schaltflächen darstellen, die dem Benutzer angezeigt werden.

Im folgenden Codebeispiel wird demonstriert, wie Sie eine Nachricht senden, die dem Benutzer drei mögliche Aktionen vorschlägt:

[!code-javascript[Send suggested actions](../includes/code/node-send-suggested-actions.js#sendSuggestedActions)]

Wenn der Benutzer auf eine der vorgeschlagenen Aktionen tippt, empfängt der Bot vom Benutzer eine Nachricht mit dem `value` der entsprechenden Aktion.

Beachten Sie, dass die `imBack`-Methode den `value` an das Chatfenster des Kanals sendet, den Sie verwenden. Wenn Sie dies ändern möchten, können Sie die `postBack`-Methode verwenden, wodurch zwar immer noch die Auswahl zurück an Ihren Bot gesendet, aber nicht im Chatfenster angezeigt wird. Einige Kanäle unterstützen `postBack` nicht. Allerdings verhält sich die Methode in diesen Instanzen wie `imBack`.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Funktionsvorschau mit Channel Inspector][inspector]
* [IMessage][IMessage]
* [ICardAction][ICardAction]
* [session.send][SessionSend]

[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage

[SessionSend]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#send

[ICardAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.icardaction.html

[inspector]: ../bot-service-channel-inspector.md

[channelInspector]: ../bot-service-channel-inspector.md
