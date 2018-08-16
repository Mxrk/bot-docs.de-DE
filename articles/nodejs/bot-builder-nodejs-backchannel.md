---
title: Austauschen von Informationen mithilfe des Websteuerelements | Microsoft-Dokumentation
description: Erfahren Sie etwas über das Austauschen von Informationen zwischen dem Bot und einer Webseite mithilfe des Bot Builder SDK für Node.js.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.prod: bot-framework
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b4c050ba785edcf700de4a6e4f76056464a9f649
ms.sourcegitcommit: f576981342fb3361216675815714e24281e20ddf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39298608"
---
# <a name="use-the-backchannel-mechanism"></a>Verwenden des Backchannelmechanismus

[!INCLUDE [Introduction to backchannel mechanism](../includes/snippet-backchannel.md)]

## <a name="walk-through"></a>Exemplarische Vorgehensweise

Das Open-Source-Webchat-Steuerelement greift mithilfe der JavaScript-Klasse <a href="https://github.com/microsoft/botframework-DirectLinejs" target="_blank">DirectLineJS</a> auf die Direct Line-API zu. Das Steuerelement kann eine eigene Direct Line-Instanz erstellen oder ein Instanz mit der Hostseite gemeinsam verwenden. Wenn das Steuerelement eine Direct Line-Instanz mit der Hostseite gemeinsam verwendet, können sowohl das Steuerelement als auch die Seite Aktivitäten senden und empfangen. Das folgende Diagramm zeigt die grundlegende Architektur einer Website, die Botfunktionalität über das Open-Source-Web-(Chat-)Steuerelement und die Direct Line-API unterstützt. 

![Backchannel](../media/designing-bots/patterns/back-channel.png)

### <a name="sample-code"></a>Beispielcode 

In diesem Beispiel verwenden der Bot und die Webseite den Backchannelmechanismus zum Austauschen von Informationen, die für den Benutzer nicht sichtbar sind. Der Bot fordert an, dass die Hintergrundfarbe der Webseite geändert werden soll, und die Webseite benachrichtigt den Bot, wenn der Benutzer auf der Seite auf eine Schaltfläche klickt. 

> [!NOTE]
> Die Codeausschnitte in diesem Artikel stammen aus dem <a href="https://github.com/Microsoft/BotFramework-WebChat/blob/master/samples/backchannel/index.html" target="_blank">Backchannelbeispiel</a> und dem <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">Backchannelbot</a>. 

#### <a name="client-side-code"></a>Clientseitiger Code

Zuerst erstellt die Webseite ein **DirectLine**-Objekt.

```javascript
var botConnection = new BotChat.DirectLine(...);
```

Anschließend gibt es das **DirectLine**-Objekt beim Erstellen der WebChat-Instanz frei.

```javascript
BotChat.App({
    botConnection: botConnection,
    user: user,
    bot: bot
}, document.getElementById("BotChatGoesHere"));
```

Wenn der Benutzer auf der Webseite auf eine Schaltfläche klickt, sendet die Webseite eine Aktivität vom Typ „event“, um den Bot zu benachrichtigen, dass auf die Schaltfläche geklickt wurde.

```javascript
const postButtonMessage = () => {
    botConnection
        .postActivity({type: "event", value: "", from: {id: "me" }, name: "buttonClicked"})
        .subscribe(id => console.log("success"));
    }
```

> [!TIP]
> Verwenden Sie die Attribute `name` und `value`, um alle Informationen mitzuteilen, die der Bot für die ordnungsgemäße Interpretation des Ereignisses bzw. die Antwort auf dieses möglicherweise benötigt. 

Schließlich lauscht die Webseite auch auf ein bestimmtes Ereignis vom Bot.
In diesem Beispiel lauscht die Webseite auf eine Aktivität mit dem Typ „event“ und dem Namen „changeBackground“. Wenn sie diesen Typ von Aktivität empfängt, ändert sie die Hintergrundfarbe der Webseite auf den von der Aktivität angegebenen `value`. 

```javascript
botConnection.activity$
    .filter(activity => activity.type === "event" && activity.name === "changeBackground")
    .subscribe(activity => changeBackgroundColor(activity.value))
```

#### <a name="server-side-code"></a>Serverseitiger Code

Der <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">Backchannelbot</a> erstellt ein Ereignis mit einer Hilfsfunktion.

```javascript
var bot = new builder.UniversalBot(connector, 
    function (session) {
        var reply = createEvent("changeBackground", session.message.text, session.message.address);
        session.endDialog(reply);
    }
);

const createEvent = (eventName, value, address) => {
    var msg = new builder.Message().address(address);
    msg.data.type = "event";
    msg.data.name = eventName;
    msg.data.value = value;
    return msg;
}
```

Ebenso lauscht der Bot auch auf Ereignisse vom Client. Wenn der Bot in diesem Beispiel ein Ereignis mit `name="buttonClicked"` erhält, sendet er eine Nachricht an den Benutzer, um ihn zu informieren, dass er das Klicken auf die Schaltfläche wahrgenommen hat.

```javascript
bot.on("event", function (event) {
    var msg = new builder.Message().address(event.address);
    msg.data.textLocale = "en-us";
    if (event.name === "buttonClicked") {
        msg.data.text = "I see that you clicked a button.";
    }
    bot.send(msg);
})
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Direct Line-API][directLineAPI]
- <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Microsoft Bot Framework-WebChat-Steuerelement</a>
- <a href="https://github.com/Microsoft/BotFramework-WebChat/blob/master/samples/backchannel/index.html" target="_blank">Backchannelbeispiel</a>
- <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">Backchannelbot</a>

[directLineAPI]: https://docs.botframework.com/en-us/restapi/directline3/#navtitle