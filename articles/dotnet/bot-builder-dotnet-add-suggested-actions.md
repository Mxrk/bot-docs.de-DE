---
title: Hinzufügen vorgeschlagener Aktionen zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Nachrichten mithilfe des Bot Builder SDK für .NET vorgeschlagene Aktionen hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 03/13/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 63e5f4b8ac86b6b0e29d09796dbe74295bf3e213
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39301487"
---
# <a name="add-suggested-actions-to-messages"></a>Hinzufügen von vorgeschlagenen Aktionen zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

> [!TIP]
> Verwenden Sie den [Kanalinspektor][channelInspector], wenn Sie sehen möchten, wie vorgeschlagene Aktionen auf verschiedenen Kanälen aussehen und funktionieren.

## <a name="send-suggested-actions"></a>Senden von vorgeschlagenen Aktionen

Zum Hinzufügen vorgeschlagener Aktionen zu einer Nachricht legen Sie die `SuggestedActions`-Eigenschaft der Aktivität auf die Liste der [CardAction][cardAction]-Objekte fest, die die Schaltflächen darstellen, die dem Benutzer angezeigt werden. 

Im folgenden Codebeispiel wird demonstriert, wie Sie eine Nachricht erstellen, die dem Benutzer drei vorgeschlagene Aktionen vorstellt:

[!code-csharp[Add suggested actions](../includes/code/dotnet-add-suggested-actions.cs#addSuggestedActions)]

Wenn der Benutzer auf eine der vorgeschlagenen Aktionen tippt, empfängt der Bot vom Benutzer eine Nachricht mit dem Wert (`Value`) der entsprechenden Aktion.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Featurevorschau mit dem Kanalinspektor][inspector]
- [Activities overview (Übersicht über Aktivitäten)](bot-builder-dotnet-activities.md)
- [Create messages (Erstellen von Nachrichten)](bot-builder-dotnet-create-messages.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">IMessageActivity-Schnittstelle</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">CardAction-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">SuggestedActions-Klasse</a>

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md

[channelInspector]: ../bot-service-channel-inspector.md


