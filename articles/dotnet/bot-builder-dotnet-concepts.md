---
title: Wichtige Konzepte im Bot Framework SDK für .NET | Microsoft-Dokumentation
description: Lernen Sie wichtige Konzepte und Tools zum Erstellen und Bereitstellen von Unterhaltungsbots kennen, die im Bot Framework SDK für .NET verfügbar sind.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: e01473a06a0cdbef635de33e5734b02351e36cea
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54223805"
---
# <a name="key-concepts-in-the-bot-framework-sdk-for-net"></a>Wichtige Konzepte im Bot Framework SDK für .NET

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-concepts.md)
> - [Node.js](../nodejs/bot-builder-nodejs-concepts.md)

Dieser Artikel stellt wichtige Konzepte im Bot Framework SDK für .NET vor.

## <a name="connector"></a>Connector

Der [Bot Framework-Connector](bot-builder-dotnet-connector.md) bietet eine einzelne REST-API, mit der ein Bot über verschiedene Kanäle wie Skype, Slack, E-Mail und andere kommunizieren kann. Er ermöglicht die Kommunikation zwischen Bot und Benutzer, indem Nachrichten vom Bot zum Kanal und vom Kanal zum Bot weitergeleitet werden. 

Im Bot Framework SDK für .NET ermöglicht die [Connector][connectorLibrary]-Bibliothek den Zugriff auf den Connector. 

## <a name="activity"></a>Aktivität

[!INCLUDE [Activity concept overview](../includes/snippet-dotnet-concept-activity.md)]

Weitere Informationen zu Aktivitäten im Bot Framework SDK für .NET finden Sie unter [Übersicht über Aktivitäten](bot-builder-dotnet-activities.md).

## <a name="dialog"></a>Dialog

Wenn Sie einen Bot mithilfe des Bot Framework SDK für .NET erstellen, können Sie [Dialoge](bot-builder-dotnet-dialogs.md) verwenden, um eine Konversation zu modellieren und den [Konversationsfluss](../bot-service-design-conversation-flow.md#dialog-stack) zu verwalten. Ein Dialog kann sich aus anderen Dialogen zusammensetzen, um die Wiederverwendung zu maximieren. In einem Dialogkontext wird der [Stapel von Dialogen](../bot-service-design-conversation-flow.md), die zu einem bestimmten Zeitpunkt in der Konversation aktiv sind, verwaltet. Eine Konversation, die sich aus Dialogen zusammensetzt, ist auf andere Computer übertragbar, sodass Ihre Botimplementierung skalierbar ist. 

Im Bot Framework SDK für .NET können Sie mit der [Builder][builderLibrary]-Bibliothek Dialoge verwalten.

## <a name="formflow"></a>FormFlow

Sie können [FormFlow](bot-builder-dotnet-formflow.md) im Bot Framework SDK für .NET verwenden, um die Erstellung eines Bots zu optimieren, der Informationen vom Benutzer sammelt. Beispielsweise muss ein Bot, der Sandwichbestellungen entgegennimmt, verschiedene Angaben vom Benutzer sammeln, z.B. die Brotsorte, die Auswahl des Belags, die Größe usw. Mit grundlegenden Richtlinien kann FormFlow die erforderlichen Dialoge zum Verwalten einer geführten Konversation wie dieser automatisch generieren.

## <a name="state"></a>Zustand

[!INCLUDE [State concept overview](../includes/snippet-dotnet-concept-state.md)]

Weitere Informationen zum Verwalten von Zuständen mithilfe des Bot Framework SDK für .NET finden Sie unter [Verwalten von Zustandsdaten](bot-builder-dotnet-state.md).

## <a name="naming-conventions"></a>Benennungskonventionen

Das Bot Framework SDK für die .NET-Bibliothek verwendet stark typisierte Namenskonventionen in Pascal-Schreibweise. Die JSON-Nachrichten, die hin- und zurückgesendet werden, verwenden jedoch Benennungskonventionen in Camel-Case-Schreibweise. Beispielsweise wird die C#-Eigenschaft **ReplyToId** in der gesendeten JSON-Nachricht als **replyToId** serialisiert.

## <a name="security"></a>Sicherheit

Sie sollten sicherstellen, dass der Endpunkt Ihres Bots nur durch den Bot Framework-Connectordienst aufgerufen werden kann. Weitere Informationen zu diesem Thema finden Sie unter [Schützen Ihres Bots](bot-builder-dotnet-security.md).

## <a name="next-steps"></a>Nächste Schritte

Nun kennen Sie die Konzepte hinter jedem Bot. Sie können schnell [einen Bot mithilfe von Visual Studio erstellen](bot-builder-dotnet-quickstart.md), indem Sie eine Vorlage verwenden. Vertiefen Sie als Nächstes jedes wichtige Konzept, beginnend mit Dialogen.

> [!div class="nextstepaction"]
> [Dialoge im Bot Framework SDK für .NET](bot-builder-dotnet-dialogs.md)

[connectorLibrary]: /dotnet/api/microsoft.bot.connector

[builderLibrary]: /dotnet/api/microsoft.bot.builder.dialogs
