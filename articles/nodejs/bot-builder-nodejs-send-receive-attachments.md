---
title: Senden und Empfangen von Anlagen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Nachrichten mit Anlagen mit dem Bot Framework SDK für Node.js senden und empfangen.
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 1911a5b0f8e8f8b53de6f661c0a939767df1efbb
ms.sourcegitcommit: b15cf37afc4f57d13ca6636d4227433809562f8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54224695"
---
# <a name="send-and-receive-attachments"></a>Senden und Empfangen von Anlagen

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-media-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-receive-attachments.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-media-attachments.md)

Ein Nachrichtenaustausch zwischen Benutzer und Bot kann Medienanlagen enthalten, z.B. Bilder, Videos, Audio und Dateien. Die gesendeten Anlagetypen variieren je nach Kanal, doch dies sind die Grundtypen:

* **Medien und Dateien:** Sie können Dateien wie Bilder, Audio und Video senden, indem Sie **contentType** auf den MIME-Typ des [IAttachment-Objekts][IAttachment] festlegen und dann einen Link zur Datei in **contentUrl** übergeben.
* **Karten:** Sie können einen umfangreichen Satz visueller Karten <!-- and custom keyboards --> senden, indem Sie **contentType** auf den gewünschten Kartentyp setzen und dann das JSON-Format für die Karte übergeben. Wenn Sie für Rich Card-Generatoren Klassen wie **HeroCard** verwenden, wird die Anlage automatisch für Sie ausgefüllt. Ein Beispiel finden Sie unter [Senden einer Rich Card](bot-builder-nodejs-send-rich-cards.md).

## <a name="add-a-media-attachment"></a>Hinzufügen einer Medienanlage
Das Nachrichtenobjekt wird als Instanz von [IMessage][IMessage] erwartet. Es wird empfohlen, dem Benutzer eine Nachricht als Objekt zu senden, wenn Sie eine Anlage wie ein Bild hinzufügen möchten. Verwenden Sie die Methode [session.send()][SessionSend], um Nachrichten als JSON-Objekt zu senden. 

## <a name="example"></a>Beispiel

Das folgende Beispiel überprüft, ob der Benutzer eine Anlage gesendet hat. Falls ja, wird jedes Bild in der Anlage wiedergegeben. Sie können dies mit dem Bot Framework-Emulator testen, indem Sie Ihrem Bot ein Bild schicken.

```javascript
// Create your bot with a function to receive messages from the user
var bot = new builder.UniversalBot(connector, function (session) {
    var msg = session.message;
    if (msg.attachments && msg.attachments.length > 0) {
     // Echo back attachment
     var attachment = msg.attachments[0];
        session.send({
            text: "You sent:",
            attachments: [
                {
                    contentType: attachment.contentType,
                    contentUrl: attachment.contentUrl,
                    name: attachment.name
                }
            ]
        });
    } else {
        // Echo back users text
        session.send("You said: %s", session.message.text);
    }
});
```
## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Funktionsvorschau mit Channel Inspector][inspector]
* [IMessage][IMessage]
* [Senden einer Rich Card][SendRichCard]
* [session.send][SessionSend]

[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[SendRichCard]: bot-builder-nodejs-send-rich-cards.md
[SessionSend]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#send
[IAttachment]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.iattachment.html
[inspector]: ../bot-service-channel-inspector.md
