---
title: Hinzufügen vorgeschlagener Aktionen zu Nachrichten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit dem Bot Framework SDK für .NET Nachrichten vorgeschlagene Aktionen hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/13/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ad9b791cf74c4a67515fdf8d60eab29c51f93bc2
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224742"
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

## <a name="send-suggested-actions"></a>Senden von vorgeschlagenen Aktionen

Zum Hinzufügen vorgeschlagener Aktionen zu einer Nachricht legen Sie die `SuggestedActions`-Eigenschaft der Aktivität auf die Liste der [CardAction][cardAction]-Objekte fest, die die Schaltflächen darstellen, die dem Benutzer angezeigt werden. 

Im folgenden Codebeispiel wird demonstriert, wie Sie eine Nachricht erstellen, die dem Benutzer drei vorgeschlagene Aktionen vorstellt:

[!code-csharp[Add suggested actions](../includes/code/dotnet-add-suggested-actions.cs#addSuggestedActions)]

Wenn der Benutzer auf eine der vorgeschlagenen Aktionen tippt, empfängt der Bot vom Benutzer eine Nachricht mit dem `Value` der entsprechenden Aktion.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Funktionsvorschau mit Channel Inspector][inspector]
- [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md)
- [Erstellen von Nachrichten](bot-builder-dotnet-create-messages.md)
- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">IMessageActivity-Schnittstelle</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">CardAction-Klasse</a>
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">SuggestedActions-Klasse</a>

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md

[channelInspector]: ../bot-service-channel-inspector.md


