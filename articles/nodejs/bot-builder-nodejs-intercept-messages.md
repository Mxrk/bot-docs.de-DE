---
title: Abfangen von Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Protokolle oder andere Datensätze erstellen, indem Sie den Informationsaustausch mit dem Bot Builder SDK für Node.js abfangen und verarbeiten.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/02/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 31380961f117a2b4a3ffaae3c82d682a63001c0c
ms.sourcegitcommit: 984705927561cc8d6a84f811ff24c8c71b71c76b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50965678"
---
# <a name="intercept-messages"></a>Abfangen von Nachrichten

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-middleware.md)
> - [Node.js](../nodejs/bot-builder-nodejs-intercept-messages.md)

[!INCLUDE [Introduction to message logging](../includes/snippet-message-logging-intro.md)]

## <a name="example"></a>Beispiel

Das folgende Codebeispiel zeigt, wie zwischen Benutzer und Bot ausgetauschte Nachrichten mithilfe des Konzepts der **Middleware** im Bot Builder SDK für Node.js abgefangen werden. 

Konfigurieren Sie zunächst den Handler für eingehende (`botbuilder`) und für ausgehende Nachrichten (`send`).

```javascript
server.post('/api/messages', connector.listen());
var bot = new builder.UniversalBot(connector);
bot.use({
    botbuilder: function (session, next) {
        myMiddleware.logIncomingMessage(session, next);
    },
    send: function (event, next) {
        myMiddleware.logOutgoingMessage(event, next);
    }
})
```

Implementieren Sie anschließend jeden Handler, um die zu ergreifende Aktion für jede abgefangene Nachricht zu definieren.

```javascript
module.exports = {
    logIncomingMessage: function (session, next) {
        console.log(session.message.text);
        next();
    },
    logOutgoingMessage: function (event, next) {
        console.log(event.text);
        next();
    }
}
```

Jetzt löst jede eingehende Nachricht (vom Benutzer zum Bot) `logIncomingMessage`, und jede ausgehende Nachricht (vom Bot zum Benutzer) `logOutgoingMessage` aus.
In diesem Beispiel gibt der Bot einfach einige Informationen über jede Nachricht aus, aber Sie können `logIncomingMessage` und `logOutgoingMessage` nach Bedarf aktualisieren, um die Aktionen zu definieren, die Sie für jede Nachricht ausführen möchten. 

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel, das zeigt, wie Sie Nachrichten mit dem Bot Builder SDK für Node.js abfangen und protokollieren können, finden Sie in GitHub im <a href="https://aka.ms/v3-js-capability-middlewareLogging" target="_blank">Beispiel für Middleware und Protokollierung</a>.
