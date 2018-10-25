---
title: Hinzufügen von Medienanlagen zu Nachrichten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Nachrichten mithilfe des Bot Connector-Diensts Medienanlagen hinzufügen.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: 2a2cc13020c87616799ee768fbab6e72ab81cc8b
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49997637"
---
# <a name="add-media-attachments-to-messages"></a>Hinzufügen von Medienanlagen zu Nachrichten
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-media-attachments.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-receive-attachments.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-media-attachments.md)

Bots und Kanäle tauschen in der Regel Textzeichenfolgen aus, aber einige Kanäle unterstützen auch den Austausch von Anlagen, wodurch Ihr Bot reichhaltigere Nachrichten an Benutzer senden kann. Ihr Bot kann z.B. Medienanlagen (z.B. Bilder, Videos, Audio, Dateien) und [Rich Cards](bot-framework-rest-connector-add-rich-cards.md) senden. In diesem Artikel wird beschrieben, wie Sie mit dem Bot Connector-Dienst Medienanlagen zu Nachrichten hinzufügen.

> [!TIP]
> Informationen dazu, wie Sie den Typ und die Anzahl der Anlagen bestimmen, die ein Kanal unterstützt, und wie Sie ermitteln, wie der Kanal Anlagen wiedergibt, finden Sie unter [Kanalprüfung][ChannelInspector].

## <a name="add-a-media-attachment"></a>Hinzufügen einer Medienanlage  

Um eine Medienanlage zu einer Nachricht hinzuzufügen, erstellen Sie ein Objekt [Anlage][Attachment], legen Sie die Eigenschaft `name` fest, legen Sie die Eigenschaft `contentUrl` auf die URL der Mediendatei und die Eigenschaft `contentType` auf den entsprechenden Medientyp fest (z.B. **image/png**, **audio/wav**, **video/mp4**). Geben Sie dann im Objekt [Aktivität][Activity], das Ihre Nachricht darstellt, Ihr [Anlagenobjekt][Attachment] im `attachments`-Array an. 

Das folgende Beispiel zeigt eine Anforderung, die eine Nachricht mit Text einer einzelnen Bildanlage sendet. In dieser Beispielanforderung stellt `https://smba.trafficmanager.net/apis` den Basis-URI dar. Der Basis-URI für Anforderungen, die Ihr Bot ausgibt, kann ein anderer sein. Weitere Informationen zum Festlegen des Basis-URI finden Sie unter [API-Referenz](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "text": "Here's a picture of the duck I was telling you about.",
    "attachments": [
        {
            "contentType": "image/png",
            "contentUrl": "http://aka.ms/Fo983c",
            "name": "duck-on-a-rock.jpg"
        }
    ],
    "replyToId": "5d5cdc723"
}
```

Für Kanäle, die Inlinebinärdateien eines Bildes unterstützen, können Sie die `contentUrl`-Eigenschaft der `Attachment` auf eine base64-Binärdatei des Bildes setzen (z.B. **data:image/png;base64,iVBORw0KGgo...**). Der Kanal zeigt das Bild oder die URL des Bildes neben dem Text der Nachricht an.

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "text": "Here's a picture of the duck I was telling you about.",
    "attachments": [
        {
            "contentType": "image/png",
            "contentUrl": "data:image/png;base64,iVBORw0KGgo…",
            "name": "duck-on-a-rock.jpg"
        }
    ],
    "replyToId": "5d5cdc723"
}
```

Sie können eine Video- oder Audiodatei an eine Nachricht anhängen, indem Sie das gleiche Verfahren wie oben für eine Bilddatei verwenden. Klicken Sie je nach den Kanal Video und Audio möglicherweise wiedergegeben werden Inline, oder kann als Link angezeigt werden.

> [!NOTE] 
> Ihr Bot kann auch Nachrichten empfangen, die Medienanlagen enthalten.
> Beispielsweise kann eine Nachricht, die Ihr Bot erhält, eine Anlage enthalten, wenn der Kanal es dem Benutzer ermöglicht, ein Foto zur Analyse hochzuladen oder ein Dokument zu speichern.

## <a name="add-an-audiocard-attachment"></a>Hinzufügen einer AudioCard-Anlage

Das Hinzufügen einer [AudioCard](bot-framework-rest-connector-api-reference.md#audiocard-object)- oder [VideoCard](bot-framework-rest-connector-api-reference.md#videocard-object)-Anlage ist dasselbe wie das Hinzufügen einer Medienanlage. Der folgende JSON-Code zeigt beispielsweise, wie Sie eine Soundkarte in der Medienanlage hinzufügen.

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "attachments": [
    {
      "contentType": "application/vnd.microsoft.card.audio",
      "content": {
        "title": "Allegro in C Major",
        "subtitle": "Allegro Duet",
        "text": "No Image, No Buttons, Autoloop, Autostart, Sharable",
        "media": [
          {
            "url": "https://contoso.com/media/AllegrofromDuetinCMajor.mp3"
          }
        ],
        "shareable": true,
        "autoloop": true,
        "autostart": true,
        "value": {
            // Supplementary parameter for this card
        }
      }
    }],
    "replyToId": "5d5cdc723"
}
```

Sobald der Kanal diese Anlage empfängt, startet er die Wiedergabe der Audiodatei. Wenn ein Benutzer beispielsweise mit der Audiodatei interagiert, indem er auf die Schaltfläche **Pause** klickt, sendet der Kanal einen Rückruf an den Bot mit einem JSON-Code, der ungefähr so aussieht:

```json
{
    ...
    "type": "event",
    "name": "media/pause",
    "value": {
        "url": // URL for media
        "cardValue": {
            // Supplementary parameter for this card
        }
    }
}
```

Der Medienereignisname **media/pause** wird im Feld `activity.name` angezeigt. Eine Liste aller Medienereignisnamen finden Sie in der Tabelle unten.

| Ereignis | BESCHREIBUNG |
| ---- | ---- |
| **media/next** | Der Client ist zu den nächsten Medien gesprungen. |
| **media/pause** | Der Client hat die Wiedergabe von Medien unterbrochen. |
| **media/play** | Der Client hat die Wiedergabe von Medien gestartet. |
| **media/previous** | Der Client ist zu den vorherigen Medien gesprungen. |
| **media/resume** | Der Client hat die Wiedergabe von Medien fortgesetzt. |
| **media/stop** | Der Client hat die Wiedergabe von Medien angehalten. |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)
- [Hinzufügen von Rich Cards zu Nachrichten](bot-framework-rest-connector-add-rich-cards.md)
- [Kanalprüfung][ChannelInspector]

[ChannelInspector]: ../bot-service-channel-inspector.md

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
[Attachment]: bot-framework-rest-connector-api-reference.md#attachment-object
