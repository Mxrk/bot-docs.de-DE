---
title: Schlüsselkonzepte in Bot Framework Direct Line API 3.0 | Microsoft-Dokumentation
description: Grundlegende Informationen zu Schlüsselkonzepten in Bot Framework Direct Zeile API 3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: ba5939faf95546b854703978bf7f512e7665eb3b
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39304101"
---
# <a name="key-concepts-in-direct-line-api-30"></a>Schlüsselkonzepte in Direct Line API 3.0

Sie können die Kommunikation zwischen Ihrem Bot und Ihrer eigenen Clientanwendung mithilfe der Direct Line-API aktivieren. In diesem Artikel werden die Schlüsselkonzepte in Direct Line API 3.0 eingeführt und Informationen zu den relevanten Entwicklerressourcen bereitgestellt.

## <a name="authentication"></a>Authentifizierung

Die Authentifizierung von Direct Line API 3.0-Anforderungen erfolgt entweder über einen **geheimen Schlüssel**, den Sie von der Konfigurationsseite des Direct Line-Kanals im <a href="https://dev.botframework.com/" target="_blank">Bot Framework-Portal</a> abrufen, oder mithilfe eines **Tokens**, das Sie zur Laufzeit abrufen. Weitere Informationen finden Sie unter [Authentifizierung](bot-framework-rest-direct-line-3-0-authentication.md).

## <a name="starting-a-conversation"></a>Starten einer Konversation

Direct Line-Konversationen werden explizit von Clients geöffnet und können solange ausgeführt werden, wie Bot und Client teilnehmen und über gültige Anmeldeinformationen verfügen. Weitere Informationen finden Sie unter [Starten einer Konversation](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a name="sending-messages"></a>Senden von Nachrichten

Ein Client kann unter Verwendung von Direct Line API 3.0 Nachrichten an Ihren Bot senden, indem er `HTTP POST`-Anforderungen ausgibt. Ein Client kann eine einzelne Nachricht pro Anforderung senden. Weitere Informationen finden Sie unter [Senden einer Aktivität an den Bot](bot-framework-rest-direct-line-3-0-send-activity.md).

## <a name="receiving-messages"></a>Empfangen von Nachrichten

Ein Client kann unter Verwendung von Direct Line API 3.0 Nachrichten von Ihrem Bot entweder über `WebSocket`-Stream oder durch Ausgeben von `HTTP GET`-Anforderungen empfangen. Mithilfe einer dieser beiden Techniken kann ein Client möglicherweise gleichzeitig mehrere Nachrichten vom Bot als Teil eines `ActivitySet` empfangen. Weitere Informationen finden Sie unter [Empfangen von Aktivitäten vom Bot](bot-framework-rest-direct-line-3-0-receive-activities.md).

## <a name="developer-resources"></a>Entwicklerressourcen

### <a name="client-libraries"></a>Clientbibliotheken

Bot Framework stellt Clientbibliotheken bereit, die den Zugriff auf Direct Line API 3.0 über C# und Node.js erleichtern. 

- Zum Verwenden der .NET-Clientbibliothek in einem Visual Studio-Projekt installieren Sie das `Microsoft.Bot.Connector.DirectLine` <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine" target="_blank">NuGet-Paket</a>. 

- Zum Verwenden der Node.js-Clientbibliothek installieren Sie die `botframework-directlinejs`-Bibliothek über <a href="https://www.npmjs.com/package/botframework-directlinejs" target="_blank">NPM</a> (oder <a href="https://github.com/Microsoft/BotFramework-DirectLineJS" target="_blank">laden Sie die Quelle herunter</a>).

Alternativ zur Verwendung der C#- oder Node.js-Bibliotheken können Sie Ihre eigene Clientbibliothek in der Sprache Ihrer Wahl erstellen, indem Sie die <a href="https://docs.botframework.com/en-us/restapi/directline3/swagger.json" target="_blank">Direct Line API 3.0-Swagger-Datei</a> verwenden.

### <a name="sample-code"></a>Beispielcode

Das <a href="https://github.com/Microsoft/BotBuilder-Samples" target="_blank">BotBuilder-Samples</a>-GitHub-Repository enthält mehrere Beispiele, die zeigen, wie Sie Direct Line API 3.0 mit C# und Node.js verwenden.

| Beispiel | Sprache | BESCHREIBUNG |
|----|----|----|
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLine" target="_blank">Beispiel für einen Direct Line-Bot</a> | C# | Ein Beispielbot und ein benutzerdefinierter Client, die über die Direct Line-API miteinander kommunizieren. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/core-DirectLineWebSockets" target="_blank">Beispiel für einen Direct Line-Bot (mithilfe von Client-WebSockets)</a> | C# | Ein Beispielbot und ein benutzerdefinierter Client, die über die Direct Line-API und WebSockets miteinander kommunizieren. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-DirectLine" target="_blank">Beispiel für einen Direct Line-Bot</a> | JavaScript | Ein Beispielbot und ein benutzerdefinierter Client, die über die Direct Line-API miteinander kommunizieren. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-DirectLineWebSockets" target="_blank">Beispiel für einen Direct Line-Bot (mithilfe von Client-WebSockets)</a> | JavaScript | Ein Beispielbot und ein benutzerdefinierter Client, die über die Direct Line-API und WebSockets miteinander kommunizieren. |

### <a name="web-chat-control"></a>Webchat-Steuerelement 

Bot Framework stellt ein Steuerelement bereit, mit dem Sie einen Direct Line-gestützten Bot in Ihre Clientanwendung einbetten können. Weitere Informationen finden Sie unter <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Microsoft Bot Framework, WebChat-Steuerelement</a>.
