---
title: Implementieren von kanalspezifischer Funktionalität | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie kanalspezifische Funktionalität mit der Bot Connector-API implementieren.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 12/13/2017
ms.openlocfilehash: d69013c721552483cfd38b204936cb1c7f508f82
ms.sourcegitcommit: b78fe3d8dd604c4f7233740658a229e85b8535dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49996897"
---
# <a name="implement-channel-specific-functionality"></a>Implementieren von kanalspezifischer Funktionalität

Einige Kanäle bieten Funktionen, die nicht implementiert werden können, indem nur [Nachrichtentext und Anlagen](bot-framework-rest-connector-create-messages.md) verwendet werden. Um kanalspezifische Funktionen zu implementieren, können Sie native Metadaten an einen Kanal in der Eigenschaft `channelData` des [Activity][Activity]-Objekts übergeben. Zum Beispiel kann Ihr Bot die Eigenschaft `channelData` verwenden, um Telegram anzuweisen, einen Sticker zu senden, oder um Office 365 anzuweisen, eine E-Mail zu senden.

Dieser Artikel beschreibt, wie Sie die Eigenschaft `channelData` einer Nachrichtenaktivität verwenden, um diese kanalspezifische Funktionalität zu implementieren:

| Kanal | Funktionalität |
|----|----|
| E-Mail | Senden und Empfangen einer E-Mail mit Text, Betreff und wichtigen Metadaten |
| Puffer | Senden originalgetreuer Slack-Nachrichten |
| Facebook | Natives Senden von Facebook-Benachrichtigungen |
| Telegram | Ausführen von Telegram-spezifischen Aktionen, z.B. Freigeben einer Sprachnachricht oder eines Stickers |
| Kik | Senden und Empfangen nativer Kik-Nachrichten | 

> [!NOTE]
> Der Wert der `channelData`-Eigenschaft eines [Activity][Activity]-Objekts ist ein JSON-Objekt. Die Struktur des JSON-Objekts variiert in Abhängigkeit von dem implementierten Kanal und der implementierten Funktionalität, wie nachfolgend beschrieben. 

## <a name="create-a-custom-email-message"></a>Erstellen einer benutzerdefinierten E-Mail-Nachricht

Um eine E-Mail-Nachricht zu erstellen, legen Sie die `channelData`-Eigenschaft des [Activity][Activity]-Objekts auf ein JSON-Objekt fest, das diese Eigenschaften enthält:

[!INCLUDE [Email channelData table](~/includes/snippet-channelData-email.md)]

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft für eine benutzerdefinierte E-Mail-Nachricht.

```json
"channelData":
{
    "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
    "subject": "Super awesome message subject",
    "importance": "high",
    "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
}
```

## <a name="create-a-full-fidelity-slack-message"></a>Erstellen einer originalgetreuen Slack-Nachricht

Um eine originalgetreue Slack-Nachricht zu erstellen, setzen Sie die `channelData`-Eigenschaft des [Activity][Activity]-Objekts auf ein JSON-Objekt, das <a href="https://api.slack.com/docs/messages" target="_blank">Slack-Nachrichten</a>, <a href="https://api.slack.com/docs/message-attachments" target="_blank">Slack-Anlagen</a> und/oder <a href="https://api.slack.com/docs/message-buttons" target="_blank">Slack-Schaltflächen</a> festlegt. 

> [!NOTE]
> Um Schaltflächen in Slack-Nachrichten zu unterstützen, müssen Sie **interaktive Nachrichten** aktivieren, wenn Sie [Ihren Bot mit dem Slack-Kanal verbinden](../bot-service-manage-channels.md).

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft für eine benutzerdefinierte Slack-Nachricht.

```json
"channelData": {
   "text": "Now back in stock! :tada:",
   "attachments": [
        {
            "title": "The Further Adventures of Slackbot",
            "author_name": "Stanford S. Strickland",
            "author_icon": "https://api.slack.com/img/api/homepage_custom_integrations-2x.png",
            "image_url": "http://i.imgur.com/OJkaVOI.jpg?1"
        },
        {
            "fields": [
                {
                    "title": "Volume",
                    "value": "1",
                    "short": true
                },
                {
                    "title": "Issue",
                    "value": "3",
                    "short": true
                }
            ]
        },
        {
            "title": "Synopsis",
            "text": "After @episod pushed exciting changes to a devious new branch back in Issue 1, Slackbot notifies @don about an unexpected deploy..."
        },
        {
            "fallback": "Would you recommend it to customers?",
            "title": "Would you recommend it to customers?",
            "callback_id": "comic_1234_xyz",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "recommend",
                    "text": "Recommend",
                    "type": "button",
                    "value": "recommend"
                },
                {
                    "name": "no",
                    "text": "No",
                    "type": "button",
                    "value": "bad"
                }
            ]
        }
    ]
}
```

Wenn ein Benutzer auf eine Schaltfläche in einer Slack-Nachricht klickt, erhält der Bot eine Antwortnachricht, in der die `channelData`-Eigenschaft mit einem JSON-Objekt `payload` gefüllt ist. Das `payload`-Objekt gibt den Inhalt der ursprünglichen Nachricht an und identifiziert die angeklickte Schaltfläche sowie den Benutzer, der diese angeklickt hat. 

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft in der Nachricht, die ein Bot empfängt, wenn ein Benutzer auf eine Schaltfläche in der Slack-Nachricht klickt.

```json
"channelData": {
    "payload": {
        "actions": [
            {
                "name": "recommend",
                "value": "yes"
            }
        ],
        . . .
        "original_message": "{…}",
        "response_url": "https://hooks.slack.com/actions/..."
    }
}
```

Ihr Bot kann auf diese Nachricht [normal](bot-framework-rest-connector-send-and-receive-messages.md#create-reply) antworten oder seine Antwort direkt an den Endpunkt posten, der durch die `response_url`-Eigenschaft des `payload`-Objekts angegeben ist. Weitere Informationen dazu, wann und wie Sie eine Antwort an `response_url` posten können, finden Sie im Artikel zu <a href="https://api.slack.com/docs/message-buttons" target="_blank">Slack-Schaltflächen</a>. 

## <a name="create-a-facebook-notification"></a>Erstellen einer Facebook-Benachrichtigung

Um eine Facebook-Nachricht zu erstellen, legen Sie die `channelData`-Eigenschaft des [Activity][Activity]-Objekts auf ein JSON-Objekt fest, das diese Eigenschaften angibt: 

| Eigenschaft | BESCHREIBUNG |
|----|----|
| notification_type | Der Typ der Benachrichtigung (z.B. **REGULAR**, **SILENT_PUSH**, **NO_PUSH**)
| attachment | Eine Anlage, die ein Bild, ein Video oder einen anderen Multimediatyp angibt bzw. eine auf Vorlagen basierende Anlage, z.B. eine Bestätigung |

> [!NOTE]
> Ausführliche Informationen zum Format und Inhalt der Eigenschaften `notification_type` und `attachment` finden Sie in der <a href="https://developers.facebook.com/docs/messenger-platform/send-api-reference#guidelines" target="_blank">Dokumentation zur Facebook-API</a>. 

Dieser Codeausschnitt zeigt ein Beispiel der `channelData`-Eigenschaft für eine Facebook-Anlage vom Typ „Bestätigung“.

```json
"channelData": {
    "notification_type": "NO_PUSH",
    "attachment": {
        "type": "template"
        "payload": {
            "template_type": "receipt",
            . . .
        }
    }
}
```

## <a name="create-a-telegram-message"></a>Erstellen einer Telegram-Nachricht

Um eine Nachricht zu erstellen, die Telegram-spezifische Aktionen implementiert, z.B. die Freigabe einer Sprachnotiz oder eines Stickers, setzen Sie die `channelData`-Eigenschaft des [Activity][Activity]-Objekts auf ein JSON-Objekt, das diese Eigenschaften angibt: 

| Eigenschaft | BESCHREIBUNG |
|----|----|
| method | Die aufzurufende Telegram-Bot-API-Methode |
| Parameter | Die Parameter der angegebenen Methode |

Die folgenden Telegram-Methoden werden unterstützt: 

- answerInlineQuery
- editMessageCaption
- editMessageReplyMarkup
- editMessageText
- forwardMessage
- kickChatMember
- sendAudio
- sendChatAction
- sendContact
- sendDocument
- sendLocation
- sendMessage
- sendPhoto
- sendSticker
- sendVenue
- sendVideo
- sendVoice
- unbanChateMember

Weitere Informationen zu diesen Telegram-Methoden und ihren Parametern finden Sie in der <a href="https://core.telegram.org/bots/api#available-methods" target="_blank">Telegram-Bot-API-Dokumentation</a>.

> [!NOTE]
> <ul><li>Der <code>chat_id</code>-Parameter gilt für alle Telegram-Methoden. Wenn Sie <code>chat_id</code> nicht als Parameter angeben, stellt das Framework die ID für die Sie bereit.</li>
> <li>Anstatt Dateiinhalte inline zu übergeben, geben Sie die Datei über eine URL und einen Medientyp an, wie im Beispiel unten gezeigt.</li>
> <li>In jeder Nachricht, die Ihr Bot vom Telegram-Kanal empfängt, enthält die <code>channelData</code>-Eigenschaft die Nachricht, die Ihr Bot zuvor gesendet hat.</li></ul>

Dieser Codeausschnitt zeigt ein Beispiel für eine `channelData`-Eigenschaft, die eine einzelne Telegram-Methode angibt.

```json
"channelData": {
    "method": "sendSticker",
    "parameters": {
        "sticker": {
            "url": "https://domain.com/path/gif",
            "mediaType": "image/gif",
        }
    }
}
```

Dieser Codeausschnitt zeigt ein Beispiel für eine `channelData`-Eigenschaft, die ein Telegram-Methodenarray angibt.

```json
"channelData": [
    {
        "method": "sendSticker",
        "parameters": {
            "sticker": {
                "url": "https://domain.com/path/gif",
                "mediaType": "image/gif",
            }
        }
    },
    {
        "method": "sendMessage",
        "parameters": {
            "text": "<b>This message is HTML formatted.</b>",
            "parse_mode": "HTML"
        }
    }
]
```

## <a name="create-a-native-kik-message"></a>Erstellen einer nativen Kik-Nachricht

Um eine native Kik-Nachricht zu erstellen, legen Sie die `channelData`-Eigenschaft des [Activity][Activity]-Objekts auf ein JSON-Objekt fest, das diese Eigenschaft angibt: 

| Eigenschaft | BESCHREIBUNG |
|----|----|
| Cloud an das Gerät | Ein Array von Kik-Nachrichten. Weitere Informationen zum Kik-Nachrichtenformat finden Sie im Artikel zu <a href="https://dev.kik.com/#/docs/messaging#message-formats" target="_blank">Kik-Nachrichtenformaten</a>. |

Dieser Codeausschnitt zeigt ein Beispiel für die `channelData`-Eigenschaft für eine native Kik-Nachricht.

```json
"channelData": {
    "messages": [
        {
            "chatId": "c6dd8165…",
            "type": "link",
            "to": "kikhandle",
            "title": "My Webpage",
            "text": "Some text to display",
            "url": "http://botframework.com",
            "picUrl": "http://lorempixel.com/400/200/",
            "attribution": {
                "name": "My App",
                "iconUrl": "http://lorempixel.com/50/50/"
            },
            "noForward": true,
            "kikJsData": {
                    "key": "value"
                }
        }
    ]
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Übersicht über Aktivitäten](bot-framework-rest-connector-activities.md)
- [Erstellen von Nachrichten](bot-framework-rest-connector-create-messages.md)
- [Senden und Empfangen von Nachrichten](bot-framework-rest-connector-send-and-receive-messages.md)
- [Preview features with the Channel Inspector](../bot-service-channel-inspector.md) (Funktionsvorschau mit der Kanalprüfung)

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
