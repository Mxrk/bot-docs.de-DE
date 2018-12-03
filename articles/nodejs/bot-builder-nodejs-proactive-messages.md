---
title: Senden von proaktiven Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den aktuellen Konversationsfluss mit dem Bot Builder SDK für Node.js und einer proaktiven Nachricht unterbrechen
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 4ca33d59c967bc4eebc2f88fa4ddd67a9a6af6d7
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997153"
---
# <a name="send-proactive-messages"></a>Senden von proaktiven Nachrichten
[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-proactive-messages.md)
> - [Node.js](../nodejs/bot-builder-nodejs-proactive-messages.md)

[!INCLUDE [Introduction to proactive messages - part 1](../includes/snippet-proactive-messages-intro-1.md)]

## <a name="types-of-proactive-messages"></a>Typen von proaktiven Nachrichten

[!INCLUDE [Introduction to proactive messages - part 2](../includes/snippet-proactive-messages-intro-2.md)]

## <a name="send-an-ad-hoc-proactive-message"></a>Senden einer proaktiven Ad-hoc-Nachricht

Die folgenden Codebeispiele zeigen, wie Sie mit dem Bot Builder SDK für Node.js eine proaktive Ad-hoc-Nachricht senden.

Um eine Ad-hoc-Nachricht an einen Benutzer senden zu können, muss der Bot zuerst aus der aktuellen Unterhaltung Informationen zum Benutzer sammeln und speichern. Die Eigenschaft **Adresse** der Nachricht enthält alle Informationen, die der Bot später zum Senden einer Ad-hoc-Nachricht an den Benutzer benötigt. 

```javascript
bot.dialog('adhocDialog', function(session, args) {
    var savedAddress = session.message.address;

    // (Save this information somewhere that it can be accessed later, such as in a database, or session.userData)
    session.userData.savedAddress = savedAddress;

    var message = 'Hello user, good to meet you! I now know your address and can send you notifications in the future.';
    session.send(message);
})
```

> [!NOTE]
> Der Bot kann die Benutzerdaten auf beliebige Weise speichern, solange er später darauf zugreifen kann.

Nachdem der Bot die Informationen zum Benutzer gesammelt hat, kann er jederzeit eine proaktive Ad-hoc-Nachricht an den Benutzer senden. Dazu ruft er einfach die zuvor gespeicherten Benutzerdaten ab, erstellt die Nachricht und sendet sie.

```javascript
var inMemoryStorage = new builder.MemoryBotStorage();

var bot = new builder.UniversalBot(connector)
                .set('storage', inMemoryStorage); // Register in-memory storage 

function sendProactiveMessage(address) {
    var msg = new builder.Message().address(address);
    msg.text('Hello, this is a notification');
    msg.textLocale('en-US');
    bot.send(msg);
}
```

> [!TIP]
> Eine proaktive Ad-hoc-Nachricht kann z. B. über asynchrone Trigger wie HTTP-Anforderungen, Zeitgeber, Warteschlangen oder von anderer vom Entwickler ausgewählten Stelle initiiert werden.

## <a name="send-a-dialog-based-proactive-message"></a>Senden einer dialogbasierten proaktiven Nachricht

Die folgenden Codebeispiele zeigen, wie Sie mit dem Bot Builder SDK für Node.js eine dialogbasierte proaktive Nachricht senden. Das vollständige Arbeitsbeispiel finden Sie im Ordner [Microsoft/BotBuilder-Samples/Node/core-proactiveMessages/startNewDialog](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-proactiveMessages/startNewDialog).

Um eine dialogbasierte Nachricht an einen Benutzer senden zu können, muss der Bot zuerst aus der aktuellen Unterhaltung Informationen sammeln (und speichern). Das Objekt `session.message.address` enthält alle Informationen, die der Bot zum Senden einer dialogbasierten proaktiven Nachricht an den Benutzer benötigt. 

```javascript
// proactiveDialog dialog
bot.dialog('proactiveDialog', function (session, args) {

    savedAddress = session.message.address;

    var message = 'Hey there, I\'m going to interrupt our conversation and start a survey in five seconds...';
    session.send(message);

    message = `You can also make me send a message by accessing: http://localhost:${server.address().port}/api/CustomWebApi`;
    session.send(message);

    setTimeout(() => {
        startProactiveDialog(savedAddress);
    }, 5000);
});
```

Wenn die Zeit zum Senden der Nachricht gekommen ist, erstellt der Bot einen neuen Dialog und fügt ihn am oberen Ende des Dialogstapels hinzu. Der neue Dialog übernimmt die Steuerung der Unterhaltung, übermittelt die proaktive Nachricht, wird geschlossen, und die Steuerung wird wieder an den vorherigen Dialog im Stapel übergeben. 

```javascript
// initiate a dialog proactively 
function startProactiveDialog(address) {
    bot.beginDialog(address, "*:survey");
}
```

> [!NOTE]
> Für das obige Codebeispiel ist eine benutzerdefinierte Datei (**botadapter.js**) erforderlich, die Sie [von GitHub herunterladen](https://github.com/Microsoft/BotBuilder-Samples/blob/master/Node/core-proactiveMessages/startNewDialog/botadapter.js) können.

SurveyDialog steuert die Unterhaltung bis zum Ende. Dann wird es geschlossen (durch Aufrufen von `session.endDialog()`), wodurch die Steuerung wieder an den vorherigen Dialog übergeben wird. 


```javascript
// handle the proactive initiated dialog
bot.dialog('survey', function (session, args, next) {
  if (session.message.text === "done") {
    session.send("Great, back to the original conversation");
    session.endDialog();
  } else {
    session.send('Hello, I\'m the survey dialog. I\'m interrupting your conversation to ask you a question. Type "done" to resume');
  }
});
```

## <a name="sample-code"></a>Beispielcode

Ein vollständiges Beispiel, das zeigt, wie Sie Nachrichten mit dem Bot Builder SDK für Node.js senden können, finden Sie in GitHub im <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-proactiveMessages" target="_blank">Beispiel für proaktive Nachrichten</a>. Im Beispiel für proaktive Nachrichten zeigt <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-proactiveMessages/simpleSendMessage" target="_blank">simpleSendMessage</a>, wie eine proaktive Ad-hoc-Nachricht gesendet wird, und <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-proactiveMessages/startNewDialog" target="_blank">startNewDialog</a> zeigt, wie Sie eine dialogbasierte proaktive Nachricht senden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Entwerfen des Konversationsflusses](../bot-service-design-conversation-flow.md)
