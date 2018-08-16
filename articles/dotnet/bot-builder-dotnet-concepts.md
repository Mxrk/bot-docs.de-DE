---
title: Wichtige Konzepte im Bot Builder SDK für .NET | Microsoft-Dokumentation
description: Verstehen Sie die wichtigsten Konzepte und Tools zum Erstellen und Bereitstellen von Konversationsbots, die im Bot Builder SDK für .NET verfügbar sind.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 34a4cb3623c4265b062eb66ebfb2180551ac1985
ms.sourcegitcommit: 67445b42796d90661afc643c6bb6533e9a662cbc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39574566"
---
# <a name="key-concepts-in-the-bot-builder-sdk-for-net"></a>Wichtige Konzepte im Bot Builder SDK für .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-concepts.md)
> - [Node.js](../nodejs/bot-builder-nodejs-concepts.md)

Dieser Artikel stellt wichtige Konzepte im Bot Builder SDK für .NET vor.

## <a name="connector"></a>Connector

Der [Bot Framework-Connector](bot-builder-dotnet-connector.md) bietet eine einzelne REST-API, mit der ein Bot über verschiedene Kanäle wie Skype, Slack, E-Mail und andere kommunizieren kann. Er ermöglicht die Kommunikation zwischen Bot und Benutzer, indem Nachrichten vom Bot zum Kanal und vom Kanal zum Bot weitergeleitet werden. 

Im Bot Builder SDK für .NET ermöglicht die [Connector][connectorLibrary]-Bibliothek den Zugriff auf den Connector. 

## <a name="activity"></a>Aktivität

[!INCLUDE [Activity concept overview](../includes/snippet-dotnet-concept-activity.md)]

Weitere Informationen zu Aktivitäten im Bot Builder SDK für .NET finden Sie unter [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md).

## <a name="dialog"></a>Dialog

Wenn Sie einen Bot mithilfe des Bot Builder SDK für .NET erstellen, können Sie [Dialoge](bot-builder-dotnet-dialogs.md) verwenden, um eine Konversation zu modellieren und den [Konversationsfluss](../bot-service-design-conversation-flow.md#dialog-stack) zu verwalten. Ein Dialog kann sich aus anderen Dialogen zusammensetzen, um die Wiederverwendung zu maximieren. In einem Dialogkontext wird der [Stapel von Dialogen](../bot-service-design-conversation-flow.md), die zu einem bestimmten Zeitpunkt in der Konversation aktiv sind, verwaltet. Eine Konversation, die sich aus Dialogen zusammensetzt, ist auf andere Computer übertragbar, sodass Ihre Botimplementierung skalierbar ist. 

Im Bot Builder SDK für .NET können Sie mit der [Builder][builderLibrary]-Bibliothek Dialoge verwalten.

## <a name="formflow"></a>FormFlow

Sie können [FormFlow](bot-builder-dotnet-formflow.md) im Bot Builder SDK für .NET verwenden, um die Erstellung eines Bots, der Informationen vom Benutzer sammelt, zu optimieren. Beispielsweise muss ein Bot, der Sandwichbestellungen entgegennimmt, verschiedene Angaben vom Benutzer sammeln, z.B. die Brotsorte, die Auswahl des Belags, die Größe usw. Mit grundlegenden Richtlinien kann FormFlow die erforderlichen Dialoge zum Verwalten einer geführten Konversation wie dieser automatisch generieren.

## <a name="state"></a>State (Zustand)

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

Weitere Informationen zum Verwalten von Zuständen mithilfe des Bot Builder SDK für .NET finden Sie unter [Verwalten von Zustandsdaten](bot-builder-dotnet-state.md).

## <a name="naming-conventions"></a>Benennungskonventionen

Die Bot Builder SDK für .NET-Bibliothek verwendet stark typisierte Namenskonventionen in Pascal-Schreibweise. Die JSON-Nachrichten, die hin- und zurückgesendet werden, verwenden jedoch Benennungskonventionen in Camel-Case-Schreibweise. Beispielsweise wird die C#-Eigenschaft **ReplyToId** in der gesendeten JSON-Nachricht als **replyToId** serialisiert.

## <a name="security"></a>Sicherheit

Sie sollten sicherstellen, dass der Endpunkt Ihres Bots nur durch den Bot Framework-Connectordienst aufgerufen werden kann. Weitere Informationen zu diesem Thema finden Sie unter [Schützen Ihres Bots](bot-builder-dotnet-security.md).

## <a name="next-steps"></a>Nächste Schritte

Nun kennen Sie die Konzepte hinter jedem Bot. Sie können schnell [einen Bot mithilfe von Visual Studio erstellen](bot-builder-dotnet-quickstart.md), indem Sie eine Vorlage verwenden. Vertiefen Sie als Nächstes jedes wichtige Konzept, beginnend mit Dialogen.

> [!div class="nextstepaction"]
> [Dialoge im Bot Builder SDK für .NET](bot-builder-dotnet-dialogs.md)

[connectorLibrary]: /dotnet/api/microsoft.bot.connector

[builderLibrary]: /dotnet/api/microsoft.bot.builder.dialogs
