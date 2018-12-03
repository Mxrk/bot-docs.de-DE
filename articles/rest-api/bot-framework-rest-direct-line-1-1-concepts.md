---
title: Wichtige Konzepte in der Bot Framework Direct Line-API 1.1 | Microsoft-Dokumentation
description: Grundlegende Informationen zu zentralen Konzepten in der Bot Framework Direct Line-API 1.1.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: a049d77d506fa3fa678a079de52aa424264847c9
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997047"
---
# <a name="key-concepts-in-direct-line-api-11"></a>Wichtige Konzepte in der Direct Line-API 1.1

Sie können die Kommunikation zwischen Ihrem Bot und Ihrer eigenen Clientanwendung mithilfe der Direct Line-API aktivieren. 

> [!IMPORTANT]
> In diesem Artikel werden die wichtigsten Konzepte in der Direct Line-API 1.1 vorgestellt und Informationen zu den relevanten Entwicklerressourcen angegeben. Wenn Sie eine neue Verbindung zwischen Ihrer Clientanwendung und dem Bot herstellen, verwenden Sie stattdessen [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-concepts.md).

## <a name="authentication"></a>Authentifizierung

Die Authentifizierung von Direct Line-API 1.1-Anforderungen erfolgt über einen **geheimen Schlüssel**, den Sie von der Konfigurationsseite des Direct Line-Kanals im <a href="https://dev.botframework.com/" target="_blank">Bot Framework-Portal</a> abrufen, oder mithilfe eines **Token**, das Sie zur Laufzeit abrufen.  Weitere Informationen finden Sie unter [Authentifizierung](bot-framework-rest-direct-line-1-1-authentication.md).

## <a name="starting-a-conversation"></a>Starten einer Konversation

Direct Line-Konversationen werden explizit von Clients geöffnet und können solange ausgeführt werden, wie Bot und Client teilnehmen und über gültige Anmeldeinformationen verfügen. Weitere Informationen finden Sie unter [Starten einer Konversation](bot-framework-rest-direct-line-1-1-start-conversation.md).

## <a name="sending-messages"></a>Senden von Nachrichten

Ein Client kann unter Verwendung der Direct Line-API 1.1 Nachrichten an Ihren Bot senden, indem er `HTTP POST`-Anforderungen ausgibt. Ein Client kann eine einzelne Nachricht pro Anforderung senden. Weitere Informationen finden Sie unter [Senden einer Nachricht an den Bot](bot-framework-rest-direct-line-1-1-send-message.md).

## <a name="receiving-messages"></a>Empfangen von Nachrichten

Ein Client kann unter Verwendung der Direct Line-API 1.1 Nachrichten empfangen, indem er sie über `HTTP GET`-Anforderungen abruft. Als Antwort auf jede Anforderung kann ein Client mehrere Nachrichten vom Bot als Teil eines `MessageSet` empfangen. Weitere Informationen finden Sie unter [Empfangen von Nachrichten vom Bot](bot-framework-rest-direct-line-1-1-receive-messages.md).

## <a name="developer-resources"></a>Entwicklerressourcen

### <a name="client-library"></a>Clientbibliothek

Bot Framework stellt eine Clientbibliothek bereit, die den Zugriff auf die Direct Line-API 1.1 über C# vereinfacht. Um die Clientbibliothek in einem Visual Studio-Projekt zu verwenden, installieren Sie das <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine/1.1.1" target="_blank">v1.x-NuGet-Paket</a> `Microsoft.Bot.Connector.DirectLine`. 

Alternativ zur Verwendung der C#-Clientbibliothek können Sie Ihre eigene Clientbibliothek in der Sprache Ihrer Wahl erstellen, indem Sie die <a href="https://docs.botframework.com/en-us/restapi/directline/swagger.json" target="_blank">Swagger-Datei der Direct Line-API 1.1</a> verwenden.

### <a name="web-chat-control"></a>Webchat-Steuerelement 

Bot Framework stellt ein Steuerelement bereit, mit dem Sie einen Direct Line-gestützten Bot in Ihre Clientanwendung einbetten können. Weitere Informationen finden Sie unter <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Microsoft Bot Framework, WebChat-Steuerelement</a>.
