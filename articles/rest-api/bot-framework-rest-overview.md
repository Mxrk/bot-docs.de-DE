---
title: Bot Builder-REST-APIs | Microsoft-Dokumentation
description: Erste Schritte mit den Bot-Framework-REST-APIs, die zum Erstellen von Bots und Clients, die Verbindungen mit Bots herstellen, verwendet werden können.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
ms.openlocfilehash: d6d83edb390933cb8895b26efeb9775cafdb1acb
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39302149"
---
# <a name="bot-framework-rest-apis"></a>Bot-Framework-REST-APIs
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

Mit den Bot-Framework-REST-APIs können Sie Bots erstellen, die Nachrichten mit im <a href="https://dev.botframework.com/" target="_blank">Bot-Frameworkportal</a> konfigurierten Kanälen austauschen, Statusdaten speichern und abrufen und Verbindungen Ihrer eigenen Clientanwendungen mit Ihren Bots herstellen. Alle Bot-Framework-Dienste verwenden den Branchenstandard REST und JSON über HTTPS.

## <a name="build-a-bot"></a>Erstellen eines Bots

Sie können einen Bot mit einer beliebigen Programmiersprache erstellen, wobei Sie den Bot-Connector-Dienst zum Austauschen von Nachrichten mit im Bot-Framework-Portal konfigurierten Kanälen verwenden. 

> [!TIP]
> Das Bot-Framework stellt Clientbibliotheken bereit, die zum Erstellen von Bots in C# oder Node.js verwendet werden können. Um einen Bot mit C# zu erstellen, verwenden Sie das [Bot Builder SDK für C#](../dotnet/bot-builder-dotnet-overview.md). Um einen Bot mit Node.js zu erstellen, verwenden Sie das [Bot Builder SDK für Node.js](../nodejs/index.md). 

Weitere Informationen zum Erstellen von Bots mit dem Bot-Connector-Dienst finden Sie unter [Wichtige Begriffe](bot-framework-rest-connector-concepts.md).

## <a name="build-a-client"></a>Erstellen eines Clients

Mit der Direct Line-API Sie können Ihre eigene Clientanwendung für die Kommunikation mit Ihrem Bot aktivieren. Die Direct Line-API implementiert einen Authentifizierungsmechanismus, der standardmäßige Geheimnis-/Tokenmuster verwendet und auch dann ein stabiles Schema bietet, wenn Ihr Bot die Protokollversion ändert. Weitere Informationen zur Verwendung der Direct Line-API zum Aktivieren der Kommunikation zwischen einem Client und Ihrem Bot finden Sie unter [Wichtige Begriffe](bot-framework-rest-direct-line-3-0-concepts.md). 